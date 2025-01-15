## Memory Coalescing
	- {{embed ((6774c413-0422-4d7a-9e2c-b3d108d15b60))}}
	- ### [[matrix multiplication]] example
	  collapsed:: true
		- ==Coalesce happens amongst threads, not amongst different iterations of the loop within each thread’s execution.==
		- ![access pattern](https://nichijou.co/static/d3f4c1e8944826ac08d7b974ad9718b8/93f40/pattern.jpg)
		- #### **M: unfavorable data access pattern**
		  collapsed:: true
			- fig.(A) above illustrates the data access pattern of the M array
			- threads in a warp read adjacent rows
			- during iteration 0, threads in a warp read **element 0** of rows 0 through 31.
			- during iteration 1, these same threads read **element 1** of rows 0 through 31.
			- **None** of the accesses will be coalesced.
			- ![an un-coalesced access pattern](https://nichijou.co/static/7670edec83ba2cfd4fe413f0e10b2e42/f67cc/uncoalescedP.jpg)
		- #### **N: favorable data access pattern**
		  collapsed:: true
			- fig.(B) above illustrates the data access pattern of the N array
			- each thread reads a column of N.
			- during iteration 0, threads in warp 0 read **element 1** of columns 0 through 31.
			- all these accesses will be coalesced.
			- ![a coalesced access pattern](https://nichijou.co/static/5e76a9b8f6899418b29b8cc01a01ba39/f67cc/coalescedP.jpg)
			  collapsed:: true
				- For iteration 0, The hardware detects that **these accesses are made by threads in a warp and to consecutive locations in the global memory**. It coalesces these accesses into a consolidated access. This allows the DRAMs to supply data at a high rate.
	- {{embed ((6774daac-26b3-4e93-b765-58d29c2aa4ad))}}
- ## [[Corner Turning]]
	- 把运算和加载分开，先把一个小的tiled操作数加载到shared memory。这一步要make sure我们是连续读取的，这样可以保证memory coalesce。然后运算的时候不管是怎么样的memory access pattern，都会很快，因为数据已经在shared memory了。
	- ```c++
	  
	  __global__ void MatrixMulKernel(float* M, float* N, float* P, int Width) {
	      // 定义线程块共享内存，用于存储 M 和 N 的临时 tiles
	  	__shared__ float Mds[TILE_WIDTH][TILE_WIDTH];
	  	__shared__ float Nds[TILE_WIDTH][TILE_WIDTH];
	    
	    	// 获取当前线程块的索引（块的列索引 bx 和行索引 by）
	      int bx = blockIdx.x;  // 当前线程块的列索引
	      int by = blockIdx.y;  // 当前线程块的行索引
	  
	      // 获取当前线程在块内的索引（线程的列索引 tx 和行索引 ty）
	      int tx = threadIdx.x; // 当前线程在块内的列索引
	      int ty = threadIdx.y; // 当前线程在块内的行索引
	  
	      // 确定线程负责计算的结果矩阵 P 中的元素位置
	      int Row = by * TILE_WIDTH + ty; // 当前线程负责的行索引
	      int Col = bx * TILE_WIDTH + tx; // 当前线程负责的列索引
	  
	      // 初始化线程负责计算的 P[Row][Col] 的累加结果
	      float Pvalue = 0;
	  
	      // 遍历所有需要的 tile，按列加载 M 和按行加载 N
	      for (int ph = 0; ph < Width / TILE_WIDTH; ++ph) {
	          // 将 M 的当前 tile 加载到共享内存
	          // 每个线程加载一个元素
	          Mds[ty][tx] = M[Row * Width + ph * TILE_WIDTH + tx];
	  
	          // 将 N 的当前 tile 加载到共享内存
	          // 每个线程加载一个元素
	          Nds[ty][tx] = N[(ph * TILE_WIDTH + ty) * Width + Col];
	  
	          // 确保所有线程都完成共享内存加载
	          __syncthreads();
	  
	          // 在共享内存中计算当前 tile 的部分结果
	          for (int k = 0; k < TILE_WIDTH; ++k) {
	              // 累加 M 的一行与 N 的一列的乘积
	              Pvalue += Mds[ty][k] * Nds[k][tx];
	          }
	  
	          // 同步所有线程，确保这一 tile 的计算完成后再加载下一 tile
	          __syncthreads();
	      }
	  
	      // 将计算的累加结果写回全局内存
	      P[Row * Width + Col] = Pvalue;
	  }
	  ```
	- 代码解释：
		- 假设原始是8x8的三个矩阵。
		  collapsed:: true
			- ```
			  M: 8x8
			  +------+------+------+------+------+------+------+------+
			  | M00  | M01  | M02  | M03  | M04  | M05  | M06  | M07  |
			  +------+------+------+------+------+------+------+------+
			  | M10  | M11  | M12  | M13  | M14  | M15  | M16  | M17  |
			  +------+------+------+------+------+------+------+------+
			  | M20  | M21  | M22  | M23  | M24  | M25  | M26  | M27  |
			  +------+------+------+------+------+------+------+------+
			  | M30  | M31  | M32  | M33  | M34  | M35  | M36  | M37  |
			  +------+------+------+------+------+------+------+------+
			  | M40  | M41  | M42  | M43  | M44  | M45  | M46  | M47  |
			  +------+------+------+------+------+------+------+------+
			  | M50  | M51  | M52  | M53  | M54  | M55  | M56  | M57  |
			  +------+------+------+------+------+------+------+------+
			  | M60  | M61  | M62  | M63  | M64  | M65  | M66  | M67  |
			  +------+------+------+------+------+------+------+------+
			  | M70  | M71  | M72  | M73  | M74  | M75  | M76  | M77  |
			  +------+------+------+------+------+------+------+------+
			  
			  N:8x8
			  +------+------+------+------+------+------+------+------+
			  | N00  | N01  | N02  | N03  | N04  | N05  | N06  | N07  |
			  +------+------+------+------+------+------+------+------+
			  | N10  | N11  | N12  | N13  | N14  | N15  | N16  | N17  |
			  +------+------+------+------+------+------+------+------+
			  | N20  | N21  | N22  | N23  | N24  | N25  | N26  | N27  |
			  +------+------+------+------+------+------+------+------+
			  | N30  | N31  | N32  | N33  | N34  | N35  | N36  | N37  |
			  +------+------+------+------+------+------+------+------+
			  | N40  | N41  | N42  | N43  | N44  | N45  | N46  | N47  |
			  +------+------+------+------+------+------+------+------+
			  | N50  | N51  | N52  | N53  | N54  | N55  | N56  | N57  |
			  +------+------+------+------+------+------+------+------+
			  | N60  | N61  | N62  | N63  | N64  | N65  | N66  | N67  |
			  +------+------+------+------+------+------+------+------+
			  | N70  | N71  | N72  | N73  | N74  | N75  | N76  | N77  |
			  +------+------+------+------+------+------+------+------+
			  ```
			- Block0:
				- ```
				  first loop:
				  +------+------+------+------+
				  | M00  | M01  | M02  | M03  |
				  +------+------+------+------+
				  | M10  | M11  | M12  | M13  |
				  +------+------+------+------+
				  | M20  | M21  | M22  | M23  | 
				  +------+------+------+------+
				  | M30  | M31  | M32  | M33  | 
				  +------+------+------+------+
				  
				  +------+------+------+------+
				  | N00  | N01  | N02  | N03  |
				  +------+------+------+------+
				  | N10  | N11  | N12  | N13  |
				  +------+------+------+------+
				  | N20  | N21  | N22  | N23  | 
				  +------+------+------+------+
				  | N30  | N31  | N32  | N33  | 
				  +------+------+------+------+
				  
				  first loop:
				  +------+------+------+------+
				  | N04  | N05  | N06  | N07  |
				  +------+------+------+------+
				  | M14  | M15  | M16  | M17  |
				  +------+------+------+------+
				  | M24  | M25  | M26  | M27  |
				  +------+------+------+------+
				  | M34  | M35  | M36  | M37  |
				  +------+------+------+------+
				  
				  +------+------+------+------+
				  | N40  | N41  | N42  | N43  |
				  +------+------+------+------+
				  | N50  | N51  | N52  | N53  |
				  +------+------+------+------+
				  | N60  | N61  | N62  | N63  |
				  +------+------+------+------+
				  | N70  | N71  | N72  | N73  |
				  +------+------+------+------+
				  ```
	- 未优化的代码：
		- ```
		  __global__ void matrixMulTiledUnoptimized(float* M, float* N, float* P, int Width) {
		      // 确定当前线程的全局结果矩阵位置
		      int Row = blockIdx.y * blockDim.y + threadIdx.y;
		      int Col = blockIdx.x * blockDim.x + threadIdx.x;
		  
		      // 结果值初始化
		      float Pvalue = 0.0f;
		  
		      // 遍历所有的 tile
		      for (int ph = 0; ph < Width / blockDim.x; ++ph) {
		          // 每个线程直接从全局内存加载数据（M 和 N 的当前 tile）
		          float M_element = M[Row * Width + (ph * blockDim.x + threadIdx.x)];
		          float N_element = N[(ph * blockDim.y + threadIdx.y) * Width + Col];
		  
		          // 累加结果
		          Pvalue += M_element * N_element;
		      }
		  
		      // 将结果写回全局内存
		      P[Row * Width + Col] = Pvalue;
		  }
		  ```
	- Note:
		- 这个代码仍然存在重复加载的场景。比如bx相同，by不同的时候，仍然会加载同样的M矩阵。