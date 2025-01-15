## NPU: avoid bank conflict using swizzling
	- [[weight and activation swizzling]]
- ## GPU Bank Conflict
	- ### 什么是 Bank Conflict?
		- **共享内存（Shared Memory）** 是 GPU 上的一种高速、低延迟的存储器，用于线程块内部线程共享数据。
		- 共享内存由多个 **memory bank（内存银行）** 构成，每个 bank 可以同时服务一个访问请求。
		- **Bank Conflict**（冲突）发生在多个线程同时访问同一个 bank（或不同地址但同一个 bank）时，导致存储器访问被串行化，降低性能。
	- ### Bank Conflict 的关键点
		- **共享内存分布**：
			- 共享内存是按 **地址对 bank 分配** 的，通常每个 bank 管理连续的 32 位（4 字节）地址。
			- 例如：如果有 32 个 bank，第一个 bank 存储地址 `0, 32, 64, ...`，第二个存储地址 `4, 36, 68, ...`。
		- **访问模式**：
			- 如果线程访问的数据分布在不同的 bank 上，访问是 **并行** 的（无冲突）。
			- 如果多个线程访问同一个 bank，则会发生冲突，访问被 **串行化**。
	- ### 举例说明 Bank Conflict
		- **示例 1：无冲突访问**
			- 32 个线程访问共享内存中的数据（每个线程访问一个元素）：
				- ```
				  __shared__ int shared_data[32];  // 32 个元素
				  int tid = threadIdx.x;
				  int value = shared_data[tid];    // 每个线程访问不同的地址
				  ```
			- 每个线程访问共享内存中的不同 bank，无冲突。32个bank，每个存储32-bit。存储上面实例中的32个integer元素不会引人重复的bank。
		- **示例 2：完全冲突访问**
			- 线程访问共享内存中的数据，但所有线程访问同一个 bank：
				- ```
				  __shared__ int shared_data[32];
				  int tid = threadIdx.x;
				  int value = shared_data[tid % 4];  // 访问地址被限制在 0, 1, 2, 3
				  ```
			- 由于 `tid % 4` 的结果只有 `0, 1, 2, 3`，所有线程只访问 4 个 bank，产生大量冲突。
		- **示例 3：部分冲突访问**
			- 线程访问共享内存中的数据，部分线程访问同一个 bank：
				- ```
				  __shared__ int shared_data[64];
				  int tid = threadIdx.x;
				  int value = shared_data[tid * 2];  // 访问地址被间隔分配
				  ```
			- 由于 `tid * 2` 的访问模式，使得某些线程访问相同的 bank，发生部分冲突。
	- ### Bank Conflict 如何优化？
		- #### **方法 1：调整数据布局**
			- **解决方法**：通过改变数据在共享内存中的排列方式，打破线程访问模式中的冲突。
			- **示例：引入 Padding**
				- ```
				  __shared__ int shared_data[32 + 1];  // 添加 padding
				  int tid = threadIdx.x;
				  int value = shared_data[tid * 2 + tid % 2];  // 改变访问模式
				  ```
			- 通过在共享内存中引入 padding（如在 bank 边界插入额外数据），避免多个线程访问同一个 bank。打破数据的对齐模式。
		- #### **方法 2：改变访问模式**
			- **解决方法**：通过调整线程对数据的访问顺序，使得线程访问的地址分布在不同的 bank 上。
			- **示例：调整索引计算**
				- ```
				  __shared__ int shared_data[64];
				  int tid = threadIdx.x;
				  int value = shared_data[(tid + tid / 32) % 64];  // 调整访问顺序
				  ```
			- 通过改变索引计算公式，让线程访问的地址分布均匀。
		- #### **方法 3：使用广播机制（只读共享数据）**
			- **解决方法**：当所有线程需要访问相同的数据时，利用 **广播机制**，避免冲突。
			- **示例：广播共享数据**
				- ```
				  __shared__ int shared_value;
				  int value = shared_value;  // 所有线程同时读取，无冲突
				  ```
			- 如果多个线程读取同一个地址，现代 GPU 支持广播机制，避免冲突。
		- #### **方法 4：使用寄存器（私有数据）**
		  id:: 677f4344-67e4-464f-854b-efa14a1d4c0a
			- **解决方法**：将线程独占的数据存储在寄存器中，而不是共享内存。
			- **示例：将数据存储在寄存器**
				- ```
				  int value = private_data[threadIdx.x];  // 每个线程都有独立数据
				  ```
			- 如果数据无需在线程间共享，可以完全避免共享内存访问。
	- ### 图例说明 Bank Conflict
		- #### **无冲突访问**
			- | Bank 0 | Bank 1 | Bank 2 | Bank 3 | Bank 4 | Bank 5 | ... |
			  | ---- | ---- | ---- |
			  | T0 | T1 | T2 | T3 | T4 | T5 | ... |
			- 每个线程（T0, T1, T2, ...）访问不同的 bank，无冲突。
		- #### **完全冲突访问**
			- | Bank 0 | Bank 1 | Bank 2 | Bank 3 | Bank 4 | Bank 5 | ... |
			  | ---- | ---- | ---- |
			  | T0 | T0 | T0 | T0 | T0 | T0 | ... |
			- 所有线程访问 Bank 0，产生完全冲突，访问被串行化。
			-
		- #### **部分冲突访问**
			- | Bank 0 | Bank 1 | Bank 2 | Bank 3 | Bank 4 | Bank 5 | ... |
			  | ---- | ---- | ---- |
			  | T0 | T1 | T2 | T3 | T4 | T5 | ... |
			  | T32 | T33 | T34 | T35 | T36 | T37 | ... |
			- 某些线程访问相同的 bank（如 T0 和 T32 都访问 Bank 0），部分冲突。
-