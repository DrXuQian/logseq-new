## 例子
	- #### **未粗化：每线程处理一个数据元素**
		- 假设数组长度为 16，线程块大小为 4。
		- **线程分配：**
			- T0 处理 A0
			- T1 处理 A1
			- T2 处理 A2
			- T3 处理 A3
			- ...
		- | **线程** | T0 | T1 | T2 | T3 | T4 | T5 | T6 | T7 | ... |
		  | ---- | ---- | ---- |
		  | **数据** | A0 | A1 | A2 | A3 | A4 | A5 | A6 | A7 | ... |
		- **线程数**：
			- 需要 16 个线程。
	- #### **粗化后：每线程处理 4 个数据元素**
		- 假设数组长度为 16，粗化粒度为 4。
		- **线程分配：**
			- T0 处理 A0, A1, A2, A3
			- T1 处理 A4, A5, A6, A7
			- T2 处理 A8, A9, A10, A11
			- T3 处理 A12, A13, A14, A15
		- | **线程** | T0 | T1 | T2 | T3 |
		  | ---- | ---- | ---- |
		  | **数据** | A0 | A4 | A8 | A12 |
		  |  | A1 | A5 | A9 | A13 |
		  |  | A2 | A6 | A10 | A14 |
		  |  | A3 | A7 | A11 | A15 |
		- **线程数**：
			- 需要 4 个线程，每线程处理 4 个数据元素。
- ## Code
	- ### **举例说明 Thread Coarsening**
	- #### **示例：向量加法**
		- **初始代码：单线程处理一个数据元素（未粗化）**
			- ```
			  __global__ void vector_add(float *a, float *b, float *c, int n) {
			    int tid = threadIdx.x + blockIdx.x * blockDim.x;  // 线程索引
			    if (tid < n) {
			        c[tid] = a[tid] + b[tid];  // 每个线程处理一个数据元素
			    }
			  }
			  ```
			- **线程数**：
				- 总线程数 = 数据总数 `n`（假设每个线程处理一个数据元素）。
			- **问题**：
				- 如果 `n` 很大，线程数会非常多，增加调度开销。每个线程工作量很小，寄存器和共享内存的利用率低。
		- #### **优化代码：线程粗化**
			- **改进：每线程处理多个数据元素**
			- ```
			  __global__ void vector_add_coarsened(float *a, float *b, float *c, int n, int coarsening_factor) {
			    int tid = threadIdx.x + blockIdx.x * blockDim.x;  // 线程索引
			    int stride = gridDim.x * blockDim.x;             // 网格中所有线程的跨度
			  
			    // 每个线程处理多个数据元素
			    for (int i = tid; i < n; i += stride * coarsening_factor) {
			        for (int j = 0; j < coarsening_factor; j++) {
			            if (i + j < n) {  // 确保不越界
			                c[i + j] = a[i + j] + b[i + j];
			            }
			        }
			    }
			  }
			  ```
		- **线程数**：
			- 总线程数 = `n / coarsening_factor`。
		- **粗化粒度**：
			- 每个线程处理 `coarsening_factor` 个数据元素。
		- **优点**：
			- 减少了线程总数，降低了调度和同步开销。
			- 单线程工作量增加，寄存器和共享内存利用率更高。
			- 减少线程上下文切换，提升性能。
-