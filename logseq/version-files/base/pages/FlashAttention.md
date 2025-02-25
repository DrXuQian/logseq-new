- How softmax get tiled along the reduction axis:
	- Softmax:
		- ![image.png](../assets/image_1704692081036_0.png){:height 132, :width 304}
	- https://ahmdtaha.medium.com/flashattention-fast-and-memory-efficient-exact-attention-with-io-awareness-2a0aec52ed3d
	- Toy example:
		- For the QKV in the given example, the softmax calculation can be done in a incremental manner.
		- ![](https://miro.medium.com/v2/resize:fit:700/1*2urizUOQNePhcYMWl9Fmcw.png)
		- Figure 7: A Toy Q, K, and V matrixes to illustrate the difference between standard and flash attention. (Left) Standard attention computes and stores the entire attention matrix A to compute the attention output O; (Right) Flash attention operates on individual blocks of attention matrix A (A[i]=Q[i]*K[i]). So there is no need to compute and store the entire attention matrix A.
		- ![](https://miro.medium.com/v2/resize:fit:700/1*gI4Q0tlUrRpNq4upXf4bNg.png)
		- Figure 8: An illustration for the pseudo-code from Fig. 7 applied on the toy Q, K, V matrixes. Flash attention computes exact softmax operation using summary statistics {D, and O} and without storing the attention matrix A. The official flash attention uses more statistics (e.g., m=max(row)) for numerical stability. These extra statistics are omitted for presentation purposes. D_b denotes the current block denominator/numerator, while D is a summary statistic that tracks the row denominator.
- Contexts:
	- Nvidia Ampere:
		- ![image.png](../assets/image_1693869632319_0.png)
	- Nvidia Hopper:
		- Transformer engine, fp8
	- **H**igh **B**andwidth **M**emory
		- high speed compute memory interface for 3D stacked SDRAM
		- GPU中存储单元主要有HBM和SRAM：HBM容量大但是访问速度慢，SRAM容量小却有着较高的访问速度。例如：A100 GPU有40-80GB的HBM，带宽为1.5-2.0TB/s；每108个流式多核处理器各有192KB的片上SRAM，带宽估计约为19TB/s。可以看出，片上的SRAM比HBM快一个数量级，但尺寸要小许多数量级。
- Background:
	- GPU中存储单元主要有HBM和SRAM：HBM容量大但是访问速度慢，SRAM容量小却有着较高的访问速度。例如：A100 GPU有40-80GB的HBM，带宽为1.5-2.0TB/s；每108个流式多核处理器各有192KB的片上SRAM，带宽估计约为19TB/s。可以看出，片上的SRAM比HBM快一个数量级，但尺寸要小许多数量级。
	- **综上，FlashAttention目的不是节约FLOPs，而是减少对HBM的访问。重点是FlashAttention在训练和预测过程中的结果和标准Attention一样，对用户是无感的，而其他加速方法做不到这点。**
- Original practice to run Attention on GPU:
	- ![image.png](../assets/image_1693870274360_0.png)
- Flash attention:
	- ![image.png](../assets/image_1693870255509_0.png)
	- ![image.png](../assets/image_1693870292997_0.png){:height 621, :width 1079}
	- This actually provides an insight on tiling on the reduce axis of the Softmax, if we want to use this for VPU, we need to enable convolution continue. This is because for the QK * V, if we split on the input channel, we need to output the accumulator context until the whole input channel is processed. #IDF
	- The whole process is simplified as:
		- Tile on Q and K, after Sij = Qi * Kj, calculate the SoftMax locally, and get Pij, Pij * Vj and store that to Oi.
		- Sij+1 = Qi * Kj+1, calculate SoftMax and get Pij+1, Pij+1 * Vj+1 and add that to Oi. Note that in this process, we need to update the Oi.
		- This might not be optimal for VPU, because the multiply operator might cost some time.
		- ![image.png](../assets/image_1693872044579_0.png)
	-
- Flash attention-2:
	- https://crfm.stanford.edu/2023/07/17/flash2.html
	- Recall Flash attention:
		- ![](https://crfm.stanford.edu/static/img/posts/2023-07-17-flash2/flash_recap_diagram.png)
	- Improve memory efficiency:
		- ![](https://crfm.stanford.edu/static/img/posts/2023-07-17-flash2/flash_flash2_partitioning.png)
			- For each block, FlashAttention splits K and V across 4 warps while keeping Q accessible by all warps. This is referred to as the “sliced-K” scheme. However, this is inefficient since all warps need to write their intermediate results out to shared memory, synchronize, then add up the intermediate results. These shared memory reads/writes slow down the forward pass in FlashAttention.
			- In FlashAttention-2, we instead split Q across 4 warps while keeping K and V accessible by all warps. After each warp performs matrix multiply to get a slice of Q K^T, they just need to multiply with the shared slice of V to get their corresponding slice of the output. There is no need for communication between warps. The reduction in shared memory reads/writes yields speedup