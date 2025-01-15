- Greedy
	- 通常Transformer预测时，使用`torch.argmax`取最大概率的下标做为预测的Token
	- ```
	  # Greedy Generation
	  idx = X[1,:10].reshape(1, 10) # generate 1
	  print("预测词表大小", model.config.vocab_size) # BPE词表
	  print("输入X:", idx)
	  print("输入X长度:", idx.shape)
	  for _ in range(256):
	      logits, _ = model(idx)
	      print('输出Logits:', logits.shape)     
	      probs = F.softmax(logits, dim=-1)
	      print('输出Probs:', probs.shape)
	      print(probs)
	      idx_next = torch.argmax(probs , dim=2) 
	      print('输出下一个Token:', idx_next.shape)
	      print(idx_next)
	      idx = torch.cat((idx, idx_next), dim=1)
	      print("当前的token长度:", len(idx[0]))
	      print("当前的token序列:", idx)
	      break
	  ```
- Sampling
	- 在Greedy方式中，会取最大概率值的token作为预测值。 而使用sample generation
	- **非最大概率值的token**有概率能被采样到。"冬"的token的概率只有0.2，仍有几率被采样(torch.multinomial)到。
	- ```
	  # Generation sample
	  idx = X[1,:10].reshape(1, 10) # generate 1
	  for _ in range(256):
	      logits, _ = model(idx)   
	      probs = F.softmax(logits, dim=-1)
	  #   idx_next = torch.argmax(probs , dim=2) 
	      idx_next = torch.multinomial(probs, num_samples=1) # do sample
	      idx = torch.cat((idx, idx_next), dim=1)  
	  ```
	- ![](https://pic3.zhimg.com/v2-88dfa44aafaffb8e27a66277f551d468_1440w.jpg)
- Temprature
	- ![](https://pic3.zhimg.com/v2-fc9bf0a6158bada637e204d3b8b8a35c_1440w.jpg)
- Top-K Sampling
	- generation时，不希望概率过低的被采样到。则将top-k外的token概率置为0
	- 以下示例仅取top-2时，**token “瓜”永远不会被采样到**
		- ![](https://pica.zhimg.com/v2-031854370d072c75dce7a6f41be1c9a8_r.jpg)
	- ![](https://picx.zhimg.com/v2-5e9b7cd0665f9ddac195b32f697efce7_1440w.jpg)
	- ```
	  # GPT-2 Top-K generation
	  idx = X[1,:10].reshape(1, 10) 
	  temperature = 2.0
	  top_k = 5
	  for _ in range(256):
	      idx_cond = idx if idx.size(1) <= model.config.block_size else idx[:, -model.config.block_size:]
	      logits, _ = model(idx_cond)
	      logits = logits[:, -1, :] / temperature # 温度
	      if top_k is not None: # top-k
	          v, _ = torch.topk(logits, min(top_k, logits.size(-1))) 
	      probs = F.softmax(logits, dim=-1) 
	      # sampling multinomial
	      idx_next = torch.multinomial(probs, num_samples=1)
	      idx = torch.cat((idx, idx_next), dim=1)
	  ```
- Repetition penalty
	- 重复性惩罚主要处理logits，主要有以下处理步骤
		- 1. input里出现的token挑选出来
		- 2. 将input的token进行特定惩罚，目的是使对应token的logits数值尽可能小
		- 3. 对于正负数需不同处理使得数值越小
		- ![](https://pic1.zhimg.com/v2-900a207d49479c3647fafc437f63fdf2_1440w.jpg)
	- ![](https://pic4.zhimg.com/v2-deb1c626989196289b3aee1ef5f0ea15_1440w.jpg){:height 373, :width 748}