## 含义
	- **线程私有化**指将某些数据局限于线程的私有范围，存储在寄存器或线程的私有内存中，而不是使用共享内存或全局内存。
	- 这样可以避免线程之间的竞争，同时提升数据访问速度。
	- **寄存器**是 GPU 中最快的存储单元，但寄存器数量有限，因此不能过度使用。
- ## Why?
	- 速度快，无需同步
	- 避免[[bank conflict]]
		- {{embed ((677f4344-67e4-464f-854b-efa14a1d4c0a))}}
- ## Example
	- **1. 未优化：使用共享内存（低效）**
		- ```
		  __global__ void reduce_sum_shared(float *data, float *result, int n) {
		      __shared__ float shared_data[256];  // 共享内存
		      int tid = threadIdx.x;
		      shared_data[tid] = data[tid];
		      __syncthreads();
		  
		      // 归约操作
		      for (int stride = 1; stride < 256; stride *= 2) {
		          if (tid % (stride * 2) == 0) {
		              shared_data[tid] += shared_data[tid + stride];
		          }
		          __syncthreads();  // 每步需要同步
		      }
		  
		      if (tid == 0) {
		          *result = shared_data[0];
		      }
		  }
		  ```
		- 这里每个线程需要频繁访问共享内存并同步，导致性能下降。
	- **2. 优化：使用寄存器（高效）**
		- ```
		  __global__ void reduce_sum_private(float *data, float *result, int n) {
		    int tid = threadIdx.x;
		    float sum = 0.0f;  // 使用寄存器存储私有数据
		  
		    for (int i = tid; i < n; i += blockDim.x) {
		        sum += data[i];  // 累加到寄存器
		    }
		  
		    atomicAdd(result, sum);  // 最后使用原子操作更新全局结果
		  }
		  ```
		- 这里每个线程的累加操作完全在寄存器中完成，无需使用共享内存或同步，显著提升性能。