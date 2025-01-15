## 什么是内存事务？
	- **内存事务** 是 GPU 从全局内存（Global Memory）加载或存储数据的基本单位。
	- 一个 Warp 的 32 个线程的访存请求会被硬件尝试合并成尽可能少的内存事务，目的是最大化内存带宽利用率。
	- 合并的原则是：**如果线程访问的全局内存地址位于同一个内存块（[[memory segment]]）中，这些访问可以被合并为一个内存事务**。
- ## **一个内存事务** 发生在以下情况下：
	- #### **线程访问连续的地址，且对齐到内存段**
		- 如果 Warp 中的 32 个线程访问连续的全局内存地址（例如 `A[0]` 到 `A[31]`），并且这些地址对齐到内存段的边界，则所有线程的请求可以合并为一个内存事务。
		- **示例 1：访存模式**
			- ```cpp
			  int idx = threadIdx.x + blockIdx.x * blockDim.x;
			  float value = A[idx];  // 每个线程访问 A 数组的不同位置
			  ```
			- 假设 `blockDim.x = 32`（一个 Warp），`A` 是一个 `float` 数组（每个元素 4 字节）。
			- 线程访问的地址是连续的：`A[0]`, `A[1]`, ..., `A[31]`。
			- 总访问范围是 32 × 4 字节 = 128 字节，刚好对齐到 128 字节的内存段。
			- **结果：可以合并为一个内存事务**。
	- #### **线程访问相同的地址**
		- 如果 Warp 中的所有线程访问的是同一个地址（即广播访问），则这些请求会被合并为一个内存事务。
		- **示例 2：访存模式**
			- ```cpp
			  float value = A[0];  // 所有线程访问 A[0]
			  ```
			- 所有线程访问的是同一地址 `A[0]`。
			- **结果：可以合并为一个内存事务（广播访问）**。
- ## 什么时候是多个内存事务？
	- **多个内存事务** 发生在以下情况下：
	- #### **线程访问的地址不连续**
		- 如果 Warp 中的线程访问的内存地址不连续，则需要多个内存事务来完成这些加载或存储请求。
		- **示例 1：访存模式**
			- ```cpp
			  int idx = threadIdx.x * 2;  // 每个线程访问间隔为 2 的地址
			  float value = A[idx];
			  ```
			- 线程 0 访问 `A[0]`，线程 1 访问 `A[2]`，线程 2 访问 `A[4]`，以此类推。
			- 地址之间存在间隔，无法合并到一个内存事务。
			- **结果：需要多个内存事务**。
	- #### **线程访问的地址跨越多个内存段**
		- 如果 Warp 中的线程访问的地址跨越了多个内存段（例如，128 字节的边界），则需要多个内存事务。
		- **示例 2：访存模式**
			- ```cpp
			  int idx = threadIdx.x + blockIdx.x * blockDim.x;
			  float value = A[idx + 1];  // 每个线程访问 A 数组的偏移地址
			  ```
			- 假设 `blockDim.x = 32`，`A` 是一个 `float` 数组。
			- 线程访问的地址是 `A[1]`, `A[2]`, ..., `A[32]`。
			- 总访问范围是从 `A[1]` 到 `A[32]`，跨越了 128 字节的内存段边界。
			- **结果：需要两个内存事务**。
	- #### 地址不对齐到内存段
		- 如果线程的访问地址没有对齐到内存段（例如，128 字节的边界），则可能需要额外的内存事务。
		- **示例 3：访存模式**
			- ```cpp
			  int idx = threadIdx.x + blockIdx.x * blockDim.x;
			  float value = A[idx + 3];  // 偏移量导致地址不对齐
			  ```
			- 偏移量 `+3` 导致地址从 `A[3]` 开始，不对齐到 128 字节边界。
			- **结果：需要多个内存事务**。
	- #### 线程访问随机地址
		- **示例 4：访存模式**
			- ```cpp
			  int idx = some_random_function(threadIdx.x);
			  float 	value = A[idx];
			  ```
- ## 内存优化
	- #### **保证内存访问的连续性**
		- 尽量让 Warp 中的线程访问连续的内存地址。
	- #### **保证对齐到内存段**
		- [[Memory Coalescing]]
			- ### note on alignment
			  id:: 6774daac-26b3-4e93-b765-58d29c2aa4ad
				- When accessing words of size 1, 2, 4, 8, or 16 bytes and are aligned, compiler translates this to 1 global memory instruction.
				- But if size and alignment requirements are not met, compiler would translate it to multiple global memory access instructions.
				- You can enforce the compiler to align using `__align(x)__`, for example: `struct __align(16)__{...};`
		- 确保数据结构对齐到内存段大小（例如 128 字节）。
		- 使用 CUDA 的 `__align__` 或 `__restrict__` 关键字来优化对齐。
	- #### **使用共享内存**
		- 将频繁访问的数据从全局内存加载到共享内存（Shared Memory），减少对全局内存的访问。
	- #### **合理划分线程和数据**
		- 根据数据的布局调整线程的索引计算，确保 Warp 的线程访问模式适配内存合并机制。