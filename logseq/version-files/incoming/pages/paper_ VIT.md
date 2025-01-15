---
title: paper: VIT
---

- Metadata
	- [[Class]]: [[Attention]][[Transformer]]
	- [[Topic]]: ["未来"的经典之作ViT：transformer is all you need! - 知乎](https://zhuanlan.zhihu.com/p/356155277)
	- [[Lecturer]]: https://www.zhihu.com/people/xiaohuzc
	- [[Date]]: [[February 22nd, 2022]]
	- [[Keywords]]: [[什么是vision transfomer]]
- Recall Questions
	- 简单描述VIT？#ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:32.367Z
	  card-last-reviewed:: 2023-01-28T15:13:32.367Z
	  card-last-score:: 5
	- Structure of VIT? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:37.177Z
	  card-last-reviewed:: 2023-01-28T15:13:37.178Z
	  card-last-score:: 5
		- ![](../assets/QBnrC1L8TP.png)
	- What is the patch embedding in vit?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.46
	  card-next-schedule:: 2023-02-01T15:14:09.866Z
	  card-last-reviewed:: 2023-01-28T15:14:09.866Z
	  card-last-score:: 5
	  id:: 6225c19e-a51a-4694-9d1c-ab1853502c4a
		- suppose the input feature map is $$H*W*C$$, we need to split the input to $$P*P$$ patchs.
		- Then the input is shaped like ($$N*P*P*C$$), where $$N = HW/P^2$$ patchs, 9 in this case.
		- For each $$P*P*C$$ input patch, we need to map them to $$1*D$$, where $$D$$ is the constant latent vector size used throughout in transformer.
			- We can use a $$PPC*D$$ linear projection to map the input patch to a $$1*D$$ output.
			- This is equivilant to performing a $$P*P$$ convolution on input image with $$P$$ as stride.
				- [vision_transformer/models.py at main · google-research/vision_transformer](https://github.com/google-research/vision_transformer/blob/main/vit_jax/models.py#L257)
		- ```python
		  class PatchEmbed(nn.Module):
		      """ Image to Patch Embedding
		      """
		      def __init__(self, img_size=224, patch_size=16, in_chans=3, embed_dim=768):
		         super().__init__()
		         img_size = to_2tuple(img_size)
		         patch_size = to_2tuple(patch_size)
		         num_patches = (img_size[1] // patch_size[1]) * (img_size[0] // patch_size[0])
		         self.img_size = img_size
		         self.patch_size = patch_size
		         self.num_patches = num_patches
		  
		    	   self.proj = nn.Conv2d(in_chans, embed_dim, kernel_size=patch_size, stride=patch_size)
		  
		      def forward(self, x):
		         B, C, H, W = x.shape
		         # FIXME look at relaxing size constraints
		         assert H == self.img_size[0] and W == self.img_size[1], \
		             f"Input image size ({H}*{W}) doesn't match model ({self.img_size[0]}*{self.img_size[1]})."
		         x = self.proj(x).flatten(2).transpose(1, 2)
		         return x
		  
		  ```
	- What is the positional embedding in vit? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:28.947Z
	  card-last-reviewed:: 2023-01-28T15:13:28.947Z
	  card-last-score:: 5
		- VIT 中默认采用1d的positional embedding，并且与上图中的patch embedding求和
		- ```python
		  # 这里多1是为了后面要说的class token，embed_dim即patch embed_dim
		  self.pos_embed = nn.Parameter(torch.zeros(1, num_patches + 1, embed_dim)) 
		  
		  # patch emded + pos_embed
		  x = x + self.pos_embed
		  ```
	- What is the class token in vit?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:10:41.440Z
	  card-last-reviewed:: 2023-01-28T15:10:41.440Z
	  card-last-score:: 5
		- The class token in of size embed_dims and the final class probability is calculated throught a linear classifier.
			- [vision_transformer/models.py at main · google-research/vision_transformer](https://github.com/google-research/vision_transformer/blob/main/vit_jax/models.py#L272)
		- ```python
		  # 随机初始化
		  self.cls_token = nn.Parameter(torch.zeros(1, 1, embed_dim))
		  
		  # Classifier head
		  self.head = nn.Linear(self.num_features, num_classes) if num_classes > 0 else nn.Identity()
		  
		  # 具体forward过程
		  B = x.shape[0]
		  x = self.patch_embed(x)
		  cls_tokens = self.cls_token.expand(B, -1, -1)  # stole cls_tokens impl from Phil Wang, thanks
		  x = torch.cat((cls_tokens, x), dim=1)
		  x = x + self.pos_embed
		  ```
	- what is the encoder structure in vit?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:34.478Z
	  card-last-reviewed:: 2023-01-28T15:12:34.479Z
	  card-last-score:: 5
		- same as [[Transformer]]
			- [vision_transformer/models.py at main · google-research/vision_transformer](https://github.com/google-research/vision_transformer/blob/main/vit_jax/models.py#L104)
		- ```python
		  class Attention(nn.Module):
		      def __init__(self, dim, num_heads=8, qkv_bias=False, qk_scale=None, attn_drop=0., proj_drop=0.):
		         super().__init__()
		         self.num_heads = num_heads
		         head_dim = dim // num_heads
		  
		         self.scale = qk_scale or head_dim ** -0.5
		  
		         self.qkv = nn.Linear(dim, dim * 3, bias=qkv_bias)
		         self.attn_drop = nn.Dropout(attn_drop)
		         self.proj = nn.Linear(dim, dim)
		         # 这里包含了dropout
		         self.proj_drop = nn.Dropout(proj_drop)
		  
		      def forward(self, x):
		         B, N, C = x.shape
		         qkv = self.qkv(x).reshape(B, N, 3, self.num_heads, C // self.num_heads).permute(2, 0, 3, 1, 4)
		         q, k, v = qkv[0], qkv[1], qkv[2]   # make torchscript happy (cannot use tensor as tuple)
		  
		         attn = (q @ k.transpose(-2, -1)) * self.scale
		         attn = attn.softmax(dim=-1)
		         attn = self.attn_drop(attn)
		  
		         x = (attn @ v).transpose(1, 2).reshape(B, N, C)
		         x = self.proj(x)
		         x = self.proj_drop(x)
		         return x
		  
		  ```
	- What is the MLP layer in VIT?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.7
	  card-next-schedule:: 2023-02-01T15:14:11.674Z
	  card-last-reviewed:: 2023-01-28T15:14:11.675Z
	  card-last-score:: 5
		- [vision_transformer/models.py at main · google-research/vision_transformer](https://github.com/google-research/vision_transformer/blob/main/vit_jax/models.py#L68)
		- 第一个FC先将feature宽度从$$D$$变成$$4D$$
		- 第二个FC将feature宽度从$$4D$$还原成$$D$$
		- ```python
		  class Mlp(nn.Module):
		      def __init__(self, in_features, hidden_features=None, out_features=None, act_layer=nn.GELU, drop=0.):
		         super().__init__()
		         out_features = out_features or in_features
		         hidden_features = hidden_features or in_features
		         self.fc1 = nn.Linear(in_features, hidden_features)
		         self.act = act_layer()
		         self.fc2 = nn.Linear(hidden_features, out_features)
		         self.drop = nn.Dropout(drop)
		  
		      def forward(self, x):
		         x = self.fc1(x)
		         x = self.act(x)
		         x = self.drop(x)
		         x = self.fc2(x)
		         x = self.drop(x)
		         return x
		  
		  ```
	- What is [[inductive bias]]? What is the inductive bias difference between vision transformer and convolution?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:16.896Z
	  card-last-reviewed:: 2023-01-28T15:11:16.897Z
	  card-last-score:: 5
		-
- Notes
	-
- Summary
	- Talk is Cheap
		- [vision_transformer/models.py at main · google-research/vision_transformer](https://github.com/google-research/vision_transformer/blob/main/vit_jax/models.py#L1)
	- 运算分析
		- 1. 提取图像的patch embedding与class token拼在一起加上positional embedding
			- 可以看作是PXP的convolution
				- Patch size：模型输入的patch size，ViT中共有两个设置：14x14和16x16，这个只影响计算量；
		- 2. (MSA + MLP) X N (linear + softmax)
			- gelu in MLP
		- 3. Linear classification on class token (linear)
	- ![](../assets/sj8MhwzWSB.png)
	- experiment results
		- 直接在ImageNet上训练，同level的ViT模型效果要差于ResNet，但是如果在比较大的数据集上petraining，然后再finetune，效果可以超越ResNet。比如ViT在Google私有的300M JFT数据集上pretrain后，在ImageNet上的最好Top-1 acc可达88.55%，这已经和ImageNet上的SOTA相当了
		- ![](../assets/2tXWZbrwgi.png)