---
title: 专栏：GEMM optimization
---

- https://zhuanlan.zhihu.com/p/395020419


- What is cache and register?
	 - [[cache and register]]

- What is gemm?
	 - [[Im2col]]

	 - [[GEMM in qnnpack]]

- What is blocking?
	 - [[Loop nest optimization]]

- How C++ does array multiplication?
	 - 
```c++
for(int i =0;i< A.length;i++)
{
    A[i] = A[i] * B[i];
}
```

- Github repo [How to optimize GEMM.](https://github.com/flame/how-to-optimize-gemm/wiki#the-gotoblasblis-approach-to-optimizing-matrix-matrix-multiplication---step-by-step)
	 - ![](../assets/Xm53vu1H-o.png)

	 - ![](../assets/JQbUco35Zz.png)
		 - The inner loop is to calculate the corresponding element of C one by one. 1x1 --> 14 x2 --> 27 x 3 --> ... , at this meantime, the multiplier results are added to the C accumulator.

		 - The problem to this method is that A is not reused in any form. So the constant loading of A to L1 cache would result in a low computation efficiency. For example, if we can only load 4 cache blocks in L1 cache for A matrix, then the first 4 row of A(each with 4 elements) would fullfill the cache, when we load the fifth line, the first cache line would drop. Next time, it will need to reload in L1 cache. 

		 - https://github.com/flame/how-to-optimize-gemm/wiki/Optimization2

	 - ![](../assets/KP_lJneWmF.png)
		 - The inner loop calculate a 1x4 block of C and then the next block with row+1
			 - The inner loop is actually the same as first one. Just unloop the inner loop and show that the first row of A is used for four times when calculating the 1x4 block. And the next it will calculate the next 1x4 with row+1, which is also different.

			 - This cost the same time since there is no memory benefit from this register wise.

			 - This way, A is reused more often, B also improved locality of reference because currently we are accessing 1 row of 4 elements in a loop, this might result in some improvement on speed if we can not store all N x 4 element in L1 cache

			 - Cache line size 64Byte, thus 8 fp64, so we might get further improvement computing 1 row of 8 elements (probably not on our local machine since it have a big L1 cache)

			 - https://github.com/flame/how-to-optimize-gemm/wiki/Optimization_1x4_5

	 - ![](../assets/GCAY9b8G2R.png)
		 - Keep the four elements of C in register and keep the element of A needed to calculate C in register

		 - https://github.com/flame/how-to-optimize-gemm/wiki/Optimization_1x4_9

	 - ![](../assets/EZftOgq56I.png)

	 - Reuse the elements of A, and keep B in register. The inner loop calculates the following
		 - 
```python
1 x 41, 1 x 51, 1 x 61, 1 x 71
2 x 42, 2 x 52, 2 x 62, 2 x 72
3 x 43, 3 x 53, 3 x 63, 3 x 72
4 x 44, 4 x 54, 4 x 64, 4 x 74
```

		 - 4 x 1 block of A and 1 x 4 block of B --> 4 x4 block of C

		 - one thought is that if we keep using the same element as operand, like A, it might be still in register

		 - https://github.com/flame/how-to-optimize-gemm/blob/master/src/MMult_4x4_8.c

	 - ![](../assets/0E8x9WiGHo.png)
		 - Use the vector registers and vector operations. 

		 - I am wondering if I can use a larger register size with AVX 512 instruction sets or AVX 2 instruction sets.

		 - https://github.com/flame/how-to-optimize-gemm/blob/master/src/MMult_4x4_10.c

	 - ![](../assets/mqxIu_knk2.png)
		 - What we noticed is that for all optimizations so far, performance degraded considerably when the matrices involved were much bigger than could fit in the L2 cache. In this optimization, we create an extra level of blocking to overcome this. We now have a main routine that calls what is the inner kernel used by the GotoBLAS and BLIS, and then the AddDot4x4 routine is the micro-kernel used by BLIS.

		 - Suppose the sliced part takes m and k for A, the total amount of memory in the cache at once would be m x N for C and m x k for A and N x k for B. Total would add up to m x N + m x k + N x k.

		 - How do you know the sliced matrix of A and B and C will fit in L2 cache? Or you just slice it small enough to make sure it will fit in.

		 - https://github.com/flame/how-to-optimize-gemm/wiki/Optimization_4x4_11

	 - ![](../assets/7BHOfNPs_v.png)
		 - Flatten the memory so we can get more L1 cache hit.
			 - The final inner loop as rectangled in red is using the registers for multiplication and there is nothing faster.
				 - ![](../assets/SxBW5DATOW.png)

				 - 
```python
1 2 x 1 1, 1 2 x 2 2, 1 2 x 3 3, 1 2 x 4 4
3 4 x 1 1, 3 4 x 2 2, 3 4 x 3 3, 3 4 x 4 4
```

			 - The loop a level higher, which is the inner loop 4x4 dot operation
				 - ![](../assets/o1yd6o7JDu.png)

				 - Note that the final 4x4 dot operation iteratively calculate k out of the total K. And the computation is not done in a **4 x 4** manner, rather it is done in a ** 4 x k * k x 4 ** manner.

			 - The loop a level higher is loop over the rows of C, which means we are going to calculate the 4th next row of C with the 4th next row of B. The column of A used is still the same.

			 - The loop a level higher is loop over the column of C, which means we are going to calculate the 4th next column of C with the 4th next column of A. The row of B used is still the same.
				 - ![](../assets/OQIKoeUjmR.png)
