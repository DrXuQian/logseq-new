- https://zhuanlan.zhihu.com/p/424512257
- Summary:
	- 开门见山的说，PyTorch在进行深度学习训练的时候，有4大部分的显存开销，分别是模型参数(parameters)，模型参数的梯度(gradients)，优化器状态(optimizer states)以及中间激活值(intermediate activations) 或者叫中间结果(intermediate results)。
	- ```
	  1.模型定义：定义了模型的网络结构，产生模型参数；
	  **while**(你想训练):
	      2.前向传播：执行模型的前向传播，产生中间激活值；
	      3.后向传播：执行模型的后向传播，产生梯度；
	      4.梯度更新：执行模型参数的更新，第一次执行的时候产生优化器状态。
	  ```
- Activation 在forward之后需要保留在显存中，因为计算weight的gradient需要用到activation。
-