### Difference:
	- #### Inference:
	  id:: 659de6c4-ea25-4a94-b89d-c051dbb97f1f
		- **Throughput Challenges**:
			- 对于inference来说，latency至关重要， 所以用到的batch size通常不会那么大。对于小的batch size的LLM decoder的inference，基本上就是bounded by communication cost （从GPU的存储空间loading weight到GPU的register，其实应该映射到DDR到CMX，以及CMX到Register）
			- 对于某一些场景，batch size可以调的很大。但是跟训练的场景的区别是，autoregressive的模型的inference必须要等前一个token生成之后才能生成后一个token。而训练的过程中，因为所有的语料都已经预先准备好作为training的dataset，所以不需要autoregressieve的生成前面的所有token，只需要给定一段sentance，然后预测后一个token就可以了。
		- **Feasibility Challenges under Limited Resources**:
			- 有一些网络的参数量本身就很大，放不进memory。
		- 自己的补充：
			- 1. inference时，对于autoregressive model，后一次inference会依赖前一次inference的输出，增加了一层额外的限制。
			- 2. inference时对latency更为敏感，所以batch size不会那么大。对于这种场景，基本上就是bounded by communication cost of loading weights。 For small batch size, 会有一些kernel level的优化来提升memory efficiency，像是deep fusion。
	- #### Training:
		- 对于training来说，整体的收敛速度最重要，通常batch size会大很多。Training需要all-reduce gradient，这个是通信的大头。而inference不需要这部分时间。
- ### Similarity:
	- 对于大的batch size的场景，都需要用很多GPU资源，这种情况下，inference和training都需要tensor slicing和pipeline parallelism。
	- 其实就是把模型切分到能够放到一个GPU的程度，tensor slicing是切weight的input channel或者output channel, pipeline parallelism是切vertical的model。
	- 从第一性原理的角度，都是balance communication和compute，需要对系统建模的能力和分析的能力。这些地方是可以直接迁移的。
- ### System level
	- inference: model compression.. more focus on kernel level optimization, especially for NPU, communication optimization mainly for DDR to SRAM.
	- training: for larger batch, focus more on system level optimization. communication cost for intra node and inter node.