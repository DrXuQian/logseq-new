- BackGround
	- ### 1. 预训练中的注意力窗口（Attention Window）
	  id:: 6752a818-8a4b-405e-9d3f-4f0c8cbec49e
		- **注意力窗口**指的是在每次前向传播（forward pass）中，模型能够同时关注的最大Token数量。论文指出，当前主流的语言模型在预训练时通常采用固定长度的注意力窗口，例如4K（4096）Token。这意味着：
			- **固定窗口大小训练**：在预训练阶段，模型被设计为在一次前向传播中只能处理长度不超过注意力窗口大小的输入序列。任何超过这个长度的输入都会被截断或划分为多个固定长度的窗口进行处理。
	- [[MQA/GQA/MHA]]
	  collapsed:: true
		- ![](http://yongyuan.name/imgs/posts/mha_mqa_gqa.png)
		- ### Implementing MHA, MQA, and GQA:
			- This `Attention` class dynamically implements all three attention mechanisms based on `self.num_kv_heads` and `self.num_heads` i.e
			- `self.num_kv_heads = 0`will implement MHA
			- `self.num_kv_heads = 4 `will implement GQA
			- `self.num_kv_heads = 8 #Equal to num_heads`will implement MQA
			- ```python
			  class Attention(nn.Module):
			    def __init__(self, model_args: MOEConfig):
			        super().__init__()
			        d_model = model_args.d_model
			        self.num_heads = model_args.num_heads
			        self.head_dim = model_args.d_model // model_args.num_heads
			        self.num_kv_heads = (
			            model_args.num_heads if model_args.num_kv_heads == 0 else model_args.num_kv_heads
			        )
			        assert self.num_heads % self.num_kv_heads == 0
			        self.num_queries_per_kv = self.num_heads // self.num_kv_heads
			        
			        self.key = nn.Linear(d_model, self.head_dim * self.num_heads)
			        self.query = nn.Linear(d_model, self.head_dim * self.num_kv_heads)
			        self.value = nn.Linear(d_model, self.head_dim * self.num_kv_heads)
			        self.proj = nn.Linear(d_model, d_model, model_args.bias)
			        self.attn_dropout = nn.Dropout(model_args.dropout)
			        self.res_dropout = nn.Dropout(model_args.dropout)
			        self.flash_attn = hasattr(torch.nn.functional, "scaled_dot_product_attention")
			    def forward(self, x: torch.Tensor, mask: torch.Tensor, freqs_cis) -> torch.Tensor:
			        batch, seq_len, d_model = x.shape
			        k: torch.Tensor  # type hint for lsp
			        q: torch.Tensor  # ignore
			        v: torch.Tensor
			        k = self.key(x)
			        q = self.query(x)
			        v = self.value(x)
			        k = k.view(
			            batch, seq_len, -1 , self.head_dim
			        )  # shape = (B, seq_len, num_heads, head_dim)
			        q = q.view(batch, seq_len, -1, self.head_dim)
			        v = v.view(batch, seq_len, -1, self.head_dim)
			        print(q.shape)
			        print(k.shape)
			        q, k = apply_rope(q, k, freqs_cis)
			        # Grouped Query Attention
			        if self.num_kv_heads != self.num_heads:
			            k = torch.repeat_interleave(k, self.num_queries_per_kv, dim=2)
			            v = torch.repeat_interleave(v, self.num_queries_per_kv, dim=2)
			        k = k.transpose(1, 2)  # shape = (B, num_heads, seq_len, head_dim)
			        q = q.transpose(1, 2)
			        v = v.transpose(1, 2)
			        print("q.shape",q.shape)
			        print("k.shape",k.shape)
			        
			        output = F.scaled_dot_product_attention(
			            q,
			            k,
			            v,  # order impotent
			            attn_mask=None,
			            dropout_p=self.attn_dropout.p if self.training else 0.0,
			            is_causal=True,
			        )
			        # else:
			        #     attn_mtx = torch.matmul(q, k.transpose(2, 3)) / math.sqrt(self.head_dim)
			        #     attn_mtx = attn_mtx + mask[:, :, :seq_len, :seq_len]
			        #     attn_mtx = F.softmax(attn_mtx.float(), dim=-1).type_as(k)
			        #     attn_mtx = self.attn_dropout(attn_mtx)
			        #     output = torch.matmul(attn_mtx, v)  # (batch, n_head, seq_len, head_dim)
			        # restore time as batch dimension and concat heads
			        print("v.shape",v.shape)
			        print("output.shape",output.shape)
			        output = output.transpose(1, 2).contiguous().view(batch, seq_len, d_model)
			        # final projection into the residual stream
			        output = self.proj(output)
			        output = self.res_dropout(output)
			        return output
			  ```
- https://arxiv.org/pdf/2309.17453
- Overall:
	- 为什么要写这篇论文：
		- ![image.png](../assets/image_1733469142222_0.png)
		- KV Cache 存储的问题，随着token长度的二次方递增
		- 序列长度的限制，现在的回答难以泛化到超过训练序列长度的回答
- Solution in a figure:
	- 保留attention sink + window attention
	- ![image.png](../assets/image_1733469073313_0.png)
	- attention sink的来源：
		- 后面的token都对于前面的token需要计算attention score，即使前面的token没有对于后面的token有语义上的强关联。
		- ![image.png](../assets/image_1733469280783_0.png)
		- 有意思的观察：
			- 前面两层，都是关注附近的token，也就是附近token计算出来的attention score相对较高，但是后面几层都是关注first token，也就是first token的score很高。
			- ![image.png](../assets/image_1733469371451_0.png)
		- Solution:
			- Therefore, StreamingLLM simply keeps the attention sink tokens’ KV (with just 4 initial tokens sufficing) together with the sliding window’s KV to anchor the attention computation and stabilize the model’s performance.
			- ![image.png](../assets/image_1733469673987_0.png)
		- Performance evaluation:
			- ![image.png](../assets/image_1733469558296_0.png)
			- 什么是pretraining window
				- ((6752a818-8a4b-405e-9d3f-4f0c8cbec49e))