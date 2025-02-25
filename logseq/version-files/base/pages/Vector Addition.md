- ```c++
  
  // vector_add.cu
  #include <iostream>
  #include <cuda_runtime.h>
  
  // CUDA 核函数：在 GPU 上执行
  __global__ void vectorAdd(const float* A, const float* B, float* C, int N) {
      int idx = threadIdx.x + blockIdx.x * blockDim.x;
      if (idx < N) {
          C[idx] = A[idx] + B[idx];
      }
  }
  
  int main() {
      // 初始化向量大小
      int N = 1 << 20; // 1M 元素
      size_t size = N * sizeof(float);
  
      // 分配主机内存
      float *h_A = (float*)malloc(size);
      float *h_B = (float*)malloc(size);
      float *h_C = (float*)malloc(size);
  
      // 初始化输入向量
      for (int i = 0; i < N; i++) {
          h_A[i] = 1.0f;
          h_B[i] = 2.0f;
      }
  
      // 分配设备内存
      float *d_A, *d_B, *d_C;
      cudaMalloc((void**)&d_A, size);
      cudaMalloc((void**)&d_B, size);
      cudaMalloc((void**)&d_C, size);
  
      // 将主机数据复制到设备
      cudaMemcpy(d_A, h_A, size, cudaMemcpyHostToDevice);
      cudaMemcpy(d_B, h_B, size, cudaMemcpyHostToDevice);
  
      // 定义线程块和网格大小
      int blockSize = 256; // 每个线程块的线程数
      int gridSize = (N + blockSize - 1) / blockSize; // 网格大小
  
      // 启动 CUDA 核函数
      vectorAdd<<<gridSize, blockSize>>>(d_A, d_B, d_C, N);
  
      // 同步设备，确保 GPU 完成计算
      cudaDeviceSynchronize();
  
      // 将设备数据复制回主机
      cudaMemcpy(h_C, d_C, size, cudaMemcpyDeviceToHost);
  
      // 验证结果
      bool success = true;
      for (int i = 0; i < N; i++) {
          if (fabs(h_C[i] - (h_A[i] + h_B[i])) > 1e-5) {
              success = false;
              break;
          }
      }
  
      if (success) {
          std::cout << "CUDA 环境测试成功！向量加法正确。" << std::endl;
      } else {
          std::cout << "CUDA 环境测试失败！结果不正确。" << std::endl;
      }
  
      // 释放设备内存
      cudaFree(d_A);
      cudaFree(d_B);
      cudaFree(d_C);
  
      // 释放主机内存
      free(h_A);
      free(h_B);
      free(h_C);
  
      return 0;
  }
  ```
- # 代码解释：
- `#include <cuda_runtime.h>`  的作用是什么？#card
	- `#include <cuda_runtime.h>` 是用于包含 **CUDA Runtime API** 的头文件，在 CUDA GPU 编程中提供了许多核心功能。以下是它的主要作用和相关内容：
	- ### **1. 提供 CUDA Runtime API 支持**
		- CUDA Runtime API 是 CUDA 的一部分，提供了高层接口，简化 GPU 编程。
		- **常用功能：**
			- **内存管理：[[cudaMalloc]], [[cudaFree]]**
				- ```cpp
				  cudaMalloc();    // 在 GPU 上分配内存
				  cudaFree();      // 释放 GPU 的内存
				  cudaMemcpy();    // 在主机和设备之间复制数据
				  ```
			- **线程管理：**
				- ```cpp
				  cudaDeviceSynchronize(); // 等待设备上的任务完成
				  ```
			- **内置变量：**
				- `threadIdx`：线程在线程块内的索引。
				- `blockIdx`：线程块的索引。
				- `blockDim`：每个线程块的线程数。
				- `gridDim`：网格中的线程块数量。
			- **设备管理：**
				- ```cpp
				  cudaGetDeviceCount();    // 获取可用 GPU 的数量
				  cudaSetDevice();         // 设置当前使用的 GPU
				  ```
	- ### **2. 支持内核启动和动态并行**
		- CUDA 使用 `<<<blocks, threads>>>` 语法启动内核：
		- ```cpp
		  kernel<<<numBlocks, threadsPerBlock>>>(...);
		  ```
	-
- `vectorAdd<<<gridSize, blockSize>>>(d_A, d_B, d_C, N);` 中gridSize和blockSize的意义? #card
	- https://poe.com/preview/4HyPxdJBcun8UhDDY06m
	- gridSize: number of blocks per [[grid]]
	- blockSize: number of threads per [[threadblock]]
	- ![image.png](../assets/image_1735646945044_0.png)
- [[cudaMalloc]]的输入为啥是指针的指针？ #card
	- `cudaMalloc` 的输入是 **指针的指针（`void**`）**，这是因为它需要将分配的 **设备内存地址** 返回给调用者，而 C/C++ 中函数的参数传递是按值传递的，不能直接修改变量的值（如指针本身的值）。而`cudaMalloc` 需要修改调用者的指针。
- 为什么[[cudaMalloc]]用的是void **? #card
	- `cudaMalloc` 使用 `void**` 而不是特定的类型（如 `float**` 或 `int**`），`cudaMalloc` 是一个通用的内存分配函数，可以分配任意类型的设备内存。
	- 使用 `void*` 类型表示通用指针，避免为每个类型定义不同的分配函数。
- `cudaDeviceSynchronize`的作用是什么？#card
	- host端用于等待所有提交给GPU的任务完成的函数。会阻塞后续的CPU上的程序运行。
-