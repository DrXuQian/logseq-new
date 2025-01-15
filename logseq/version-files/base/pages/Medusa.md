- 一种特殊的[[speculative decoding]]
- ### 网络结构：
	- ![](https://pic4.zhimg.com/v2-7a7f7f0b117613fd1c070cc907afea9f_r.jpg)
	- 代码如下：
		- ```python
		  
		  # Medusa Block
		  class ResBlock(nn.Module):
		      def __init__(self, hidden_size):
		          super().__init__()
		          self.linear = nn.Linear(hidden_size, hidden_size) #W1
		          torch.nn.init.zeros_(self.linear.weight)
		          self.act = nn.SiLU()
		      def forward(self, x):
		          return x + self.act(self.linear(x))
		        
		  # Medusa Model
		  class MedusaModel(nn.Module):
		      def __init__(
		          self, base_model, medusa_num_heads=4, medusa_num_layers=1,
		          base_model_name_or_path=None,
		      ):
		         # LLM Model
		         self.base_model = base_model
		          # Medusa Blocks and Medusa Heads
		          self.medusa_head = nn.ModuleList(
		              [
		                  nn.Sequential(
		                      *([ResBlock(self.hidden_size)] * medusa_num_layers),
		                      nn.Linear(self.hidden_size, self.vocab_size, bias=False), # W2 dxv
		                  )
		                  for _ in range(medusa_num_heads)
		              ]
		          )
		          # ...
		  ```
	-
- ### Medusa Decoding
	- 在[Speculative Decoding](https://zhida.zhihu.com/search?content_id=240574668&content_type=Article&match_order=3&q=Speculative+Decoding&zhida_source=entity) 中, 使用 `Draft model` 来低成本获得`Next-Next-Token` , 而`Medusa`仅通过增加头层计算就能达到同样的效果
	- Medusa更大的优势在于，除了第一次Prefill外，后续可以达到边verify边生成的效果
	- #### Medusa Inference
		- 在首次推理中，可以得到各个头的预测，但无法确认Medusa Head预测的token是否正确
		- ![](https://pic4.zhimg.com/v2-9c51ebb71d2775af8dac1cee14831b2d_1440w.jpg)
		- ```
		  with torch.inference_mode():
		      input_ids = tokenizer([prompt]).input_ids
		      input_len = len(input_ids[0])
		      input_ids = torch.as_tensor(input_ids).cuda()
		      model.current_length_data.zero_() # this is for rerun
		      medusa_logits, outputs, logits = model(input_ids, output_orig = True, past_key_values=model.past_key_values)
		  print('Medusa logits shape:', medusa_logits.shape, 'logits shape:', logits.shape)
		  ```
		- 输出为
			- ```
			  Medusa logits shape: torch**.**Size([4, 1, 20, 32000]) 
			  logits shape: torch**.**Size([1, 20, 32000])
			  ```
		- 取`argmax`得到各个头的所预测出的`token`
			- ```
			  medusa_pred **=** torch**.**argmax(medusa_logits[**...**, **-**1, :], dim **=** **-**1)
			  pred **=** torch**.**argmax(logits[**...**, **-**1, :], dim **=** **-**1)
			  **print**('Base model prediction:', tokenizer**.**batch_decode(pred))
			  **print**('Medusa prediction:', tokenizer**.**batch_decode(medusa_pred))
			  preds **=** torch**.**cat([pred, medusa_pred[:, 0 ], dim **=** **-**1) *# 将用于Verify*
			  **print**('Combined prediction:', tokenizer**.**batch_decode(preds))
			  ```
		- 输出为
			- ```
			  Base model prediction: ['Once']
			  Medusa prediction: ['upon', 'ly', 'time', ',']
			  Combined prediction: ['Once', 'upon', 'ly', 'time', ',']
			  ```
	- #### Medusa Verify
		- 借助`past_key_value`, 可以将第一次预测的5个token当成`query`与20个`KV`进行前向计算，经过softmax得到5个`next token`【绿色】
		- 将5个`next token`与`query`错位校验，就能得出接受的token，如下图右上角得到`accept_length`为2的token。
			- a. 事实上仅有“upon”匹配上了，`X[0] once-> y[0] upon = x[1] upon`
			- b. 基于`once, upon` 预测出来的`a`是可以被接受的，不需要再错位校验。
		- 最精髓的是`Medusa Head`会取accept length位置的token，当成是下一轮的输入
		- ![](https://picx.zhimg.com/v2-b8751b84b8f9869dc41c6259c238fe79_1440w.jpg)
		- 我们将第一步推理结果`preds`进行修正，注意到以下代码：
			- ```
			  with torch.inference_mode():
			    medusa_logits, outputs, logits = model(preds.cuda().unsqueeze(0), output_orig = True, past_key_values = model.past_key_values)
			  
			  medusa_pred = torch.argmax(medusa_logits[..., -5:, :], dim = -1)
			  pred = torch.argmax(logits[..., :, :], dim = -1)
			  print('Base model prediction:', tokenizer.batch_decode(pred[0]))
			  print('truncated input tokens:', preds[1:].tolist())
			  print('Output tokens:', pred[0, :].tolist())
			  ```
		- 输出为
			- ```
			  Base model prediction: ['upon', 'a', 'a', ',', 'in']
			  truncated input tokens: [2501, 368, 931, 29892]
			  Output tokens: [2501, 263, 263, 29892, 297]
			  ```
		- 校验过程
			- ```
			  posterior_mask **=** (
			            preds[1:] **==** pred[0, :**-**1]
			        )**.**int() *# 错位校验*
			  accept_length **=** torch**.**cumprod(posterior_mask, dim **=** **-**1)**.**sum()**.**item() *# 得到解码接受长度*
			  cur_length **=** accept_length **+** input_len **+** 1
			  **print**('Posterior mask:', posterior_mask**.**tolist())
			  **print**('Accept length:', accept_length)
			  **print**('Current KV cache length for attention modules:', model**.**current_length_data[0]**.**item())
			  **print**('Start length:', input_len, ',current length:', cur_length)
			  *# update kv cache*
			  model**.**current_length_data**.**fill_(cur_length)
			  *# create new input*
			  preds **=** torch**.**cat([pred[:, accept_length], medusa_pred[:,0,accept_length], dim **=** **-**1)
			  **print**('Combined prediction:', tokenizer**.**batch_decode(preds))
			  ```
		- 输出结果为
			- ```
			  Posterior mask: [1, 0, 0, 1] *# 一旦中途有0就拒不接受后面的预测*
			  Accept length: 1
			  Current KV cache length **for** attention modules: 71
			  Start length: 66 ,current length: 68
			  Combined prediction: ['a', 'time', ',', 'there', 'a']
			  ```
		- 小结：verify过程只需要有`past_key_value`和`preds`，相当于第一次要需要做`Prefill`，之后就一直做verify就行了
		- ![](https://pic3.zhimg.com/v2-fda871a6c90490ec8552fe63a672d206_1440w.jpg)