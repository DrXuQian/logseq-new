---
alias: UnifiedMemory
---
- ## unified memory in GPU
	- **Unified Memory（统一内存）** 是 GPU 编程（特别是 CUDA 编程）中的一种内存管理机制。它通过统一 CPU 和 GPU 的内存空间，使得开发者在编写代码时，无需显式地管理 CPU 和 GPU 内存之间的数据传输。
	- ### **统一内存模型**：
		- CPU 和 GPU 共享一块虚拟地址空间。
		- 通过 Unified Memory，开发者可以在 CPU 和 GPU 代码中访问同一块数据，而不需要手动管理内存拷贝。
		- CUDA Runtime 和硬件负责在后台自动管理数据的传输。
	- ### Unified Memory 的核心是 **共享虚拟地址空间** 和 **按需数据分页（Page Migration）**：
		- **共享虚拟地址空间**：
			- CPU 和 GPU 都能通过同一个虚拟地址访问同一块数据。
			- 实际上，数据可能存储在主机内存（CPU 内存）或设备内存（GPU 内存）中。
		- **按需数据分页**：
			- 当 GPU 访问 Unified Memory 中的数据时，如果数据当前在主机内存中，CUDA Runtime 会将数据从主机内存迁移到设备内存。
			- 同样，当 CPU 访问 Unified Memory 中的数据时，如果数据当前在设备内存中，CUDA Runtime 会将数据从设备内存迁移到主机内存。
			- 这种机制避免了不必要的完整数据拷贝，仅在需要时迁移数据。
		- **硬件支持**：
			- 现代 GPU 硬件（如 NVIDIA Pascal 架构及更高版本）通过硬件级别的内存分页（Page Faults）和分页迁移（Page Migration）提高了 Unified Memory 的效率。
	- ### **例子**:
		- `cudaMallocManaged`来分配内存：
		- ```c++
		  #include <iostream>
		  #include <cuda_runtime.h>
		  
		  // Kernel 函数
		  __global__ void increment(int* data, int size) {
		      int idx = threadIdx.x + blockIdx.x * blockDim.x;
		      if (idx < size) {
		          data[idx] += 1;
		      }
		  }
		  
		  int main() {
		      const int N = 10;
		      int* data;
		  
		      // 使用 Unified Memory 分配内存，data 可以被 CPU 和 GPU 访问
		      cudaMallocManaged(&data, N * sizeof(int));
		  
		      // 初始化数据（在 CPU 上）
		      for (int i = 0; i < N; i++) {
		          data[i] = i;
		      }
		  
		      // 在 GPU 上执行内核
		      increment<<<1, N>>>(data, N);
		      cudaDeviceSynchronize(); // 等待 GPU 计算完成
		  
		      // 直接在 CPU 上访问数据
		      for (int i = 0; i < N; i++) {
		          std::cout << data[i] << " ";
		      }
		      std::cout << std::endl;
		  
		      // 释放统一内存
		      cudaFree(data);
		  
		      return 0;
		  }
		  ```