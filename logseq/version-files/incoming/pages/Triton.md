# Triton 一天快速入门
	- ## Triton核心概念：
		- Triton 的基本编程模型类似于 CUDA，但更加简洁。以下是核心概念：
		- ### Program（线程块）
			- 一个 Triton 程序（`program`）对应 GPU 中的一个线程块（CUDA 的 `thread block`）。
			- 通过 `tl.program_id(dim)` 来获取线程块的 ID（类似 CUDA 的 `blockIdx`）。
		- ### Thread（线程）
			- 每个线程块由多个线程组成，每个线程负责计算输出中的一个或多个元素。
			- 通过 `tl.arange(start, stop)` 分配线程块中的线程任务（类似 CUDA 的 `threadIdx`）。
		- ### Masking（屏蔽）
			- 使用屏蔽机制（`mask`）处理无效线程，防止越界访问。
		- ### 数据加载和存储
			- 使用 `tl.load` 和 `tl.store` 从全局内存加载/存储数据，类似 CUDA 的全局内存操作。
		- ### 例子：
			- Vector addition
				- ```python
				  import triton
				  import triton.language as tl
				  import torch
				  
				  # 定义 Triton 内核
				  @triton.jit
				  def vector_add_kernel(
				      x_ptr, y_ptr, output_ptr,  # 输入和输出张量的指针
				      n_elements,               # 输入向量的总元素个数
				      BLOCK_SIZE: tl.constexpr  # 每个线程块处理的元素个数
				  ):
				      # 获取线程块 ID（类似 CUDA 的 blockIdx.x）
				      block_id = tl.program_id(0)
				      # 计算当前线程块的起始偏移
				      offset = block_id * BLOCK_SIZE + tl.arange(0, BLOCK_SIZE)
				      # 判断当前线程是否越界（用于屏蔽无效线程）
				      mask = offset < n_elements
				      # 从全局内存加载数据
				      x = tl.load(x_ptr + offset, mask=mask)
				      y = tl.load(y_ptr + offset, mask=mask)
				      # 逐元素加法
				      output = x + y
				      # 将结果存储回全局内存
				      tl.store(output_ptr + offset, output, mask=mask)
				  
				  # 定义输入向量
				  x = torch.randn(1024, device='cuda', dtype=torch.float32)
				  y = torch.randn(1024, device='cuda', dtype=torch.float32)
				  
				  # 定义输出向量
				  output = torch.empty_like(x)
				  
				  # 启动内核
				  block_size = 128  # 每个线程块处理 128 个元素
				  grid_size = (x.numel() + block_size - 1) // block_size  # 线程块的数量
				  vector_add_kernel[(grid_size, )](
				      x, y, output, x.numel(), BLOCK_SIZE=block_size
				  )
				  
				  # 验证结果
				  if torch.allclose(output, x + y):
				      print("Vector addition successful!")
				  else:
				      print("Something went wrong.")
				  
				  ```
		- Matrix Multiplication
			- ```python
			  import triton
			  import triton.language as tl
			  import torch
			  
			  @triton.jit
			  def matmul_kernel(
			      A_ptr, B_ptr, C_ptr,  # 矩阵 A、B 和结果矩阵 C 的指针
			      M, N, K,             # 矩阵的维度
			      BLOCK_SIZE_M: tl.constexpr,  # 每个线程块处理 M 维度的块大小
			      BLOCK_SIZE_N: tl.constexpr,  # 每个线程块处理 N 维度的块大小
			      BLOCK_SIZE_K: tl.constexpr   # 每次加载 K 维度的块大小
			  ):
			      # 获取线程块的 ID
			      pid_m = tl.program_id(0)  # 当前线程块在 M 维度的 ID
			      pid_n = tl.program_id(1)  # 当前线程块在 N 维度的 ID
			  
			      # 计算当前线程块的起始索引
			      offset_m = pid_m * BLOCK_SIZE_M + tl.arange(0, BLOCK_SIZE_M)[:, None]
			      offset_n = pid_n * BLOCK_SIZE_N + tl.arange(0, BLOCK_SIZE_N)[None, :]
			      
			      # 初始化结果块
			      C = tl.zeros([BLOCK_SIZE_M, BLOCK_SIZE_N], dtype=tl.float32)
			  
			      # 循环累积计算
			      for k in range(0, K, BLOCK_SIZE_K):
			          # 加载 A 和 B 的块
			          A = tl.load(A_ptr + offset_m * K + (k + tl.arange(0, BLOCK_SIZE_K)), mask=(offset_m < M)[:, None])
			          B = tl.load(B_ptr + (k + tl.arange(0, BLOCK_SIZE_K)) * N + offset_n, mask=(offset_n < N)[None, :])
			          # 乘法累积
			          C += tl.dot(A, B)
			  
			      # 将结果存储回全局内存
			      tl.store(C_ptr + offset_m * N + offset_n, C, mask=(offset_m < M)[:, None] & (offset_n < N)[None, :])
			  
			  if __name__ == "__main__":
			      M, N, K = 512, 512, 512
			      A = torch.randn((M, K), device='cuda', dtype=torch.float32)
			      B = torch.randn((K, N), device='cuda', dtype=torch.float32)
			      C = torch.empty((M, N), device='cuda', dtype=torch.float32)
			  
			      BLOCK_SIZE = 16
			      grid = (M // BLOCK_SIZE, N // BLOCK_SIZE)
			      matmul_kernel[grid](
			          A, B, C, M, N, K,
			          BLOCK_SIZE_M=BLOCK_SIZE,
			          BLOCK_SIZE_N=BLOCK_SIZE,
			          BLOCK_SIZE_K=BLOCK_SIZE
			      )
			  
			      # 验证结果
			      if torch.allclose(C, torch.matmul(A, B)):
			          print("Matrix multiplication successful!")
			      else:
			          print("Something went wrong.")
			  ```