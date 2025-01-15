---
title: Loop nest optimization
---

- References
	- https://en.wikipedia.org/wiki/Loop_nest_optimization#Tiling_size
	- https://zhuanlan.zhihu.com/p/292539074
	- [[what is column-major and row-major]]
	- Loop tiling from a simple video example
		- {{youtube https //www.youtube.com/watch?v=6udqe8CkmwA&t=305s&ab_channel=VadimKarpusenko}}
		- ![](../assets/4ZbiGc-qKt.png)
- Loop tiling (reading notes of https://zhuanlan.zhihu.com/p/292539074 )
	- Fortran
		- column major [[what is column-major and row-major]]
	- case 1
		- Naïve loop
			- 假设M足够大，那么访问B(M)的时候B(1)已经不在cache中了，所以对于每一个I，B都要重新load。
			- ```plain text
			  DO I = 1, N
			    DO J = 1, M
			      A(I) = A(I) + B(J)  
			    END DO
			  END DO
			  ```
			- > 假设每个cache line能容纳b个数组元素，associativity是fully associative，则可计算出A的cache miss次数是N/b，B的cache miss次数是N*M/b。
		- loop tiling
			- 1. 将J划分为tile size 为T的小块
				- T的取值应该让B(T)载入cache时，B(1)还在cache中。
				- ```plain text
				  DO J = 1, M, T
				    DO I = 1, N
				      DO jj = J, min(J+T-1, M)
				        A(I) = A(I) + B(jj)
				      END DO
				    END DO
				  END DO
				  ```
				- A的cache miss次数是(M/T) * (N/b)，B的cache miss次数是T/b * M/T = M/b（每一轮J层循环miss一次），所以总共的cache miss是MN/(bT) + M/b，tile之前是N/b+N*M/b。在M和N的大小也相当的情况下做tiling大约能将cache miss缩小T倍。
				- > loop tiling的基本思路很简单，就是一个cache line现在被用过以后，后面什么时候还会被用，但是按循环默认的执行方式，可能到下次再被用到的时候已经被evict了。于是我们就把循环怎么重排一下，使得一个cache line在被evict之前就被再次使用。
				- > tile的本质当总的数据量无法fit in cache的时候，把数据分成一个一个tile，让每一个tile的数据fit in the cache。所以tiling一般从最内层循环开始。
			- 2. 假设我们tile I层循环
				- ```plain text
				  DO I = 1, N, T
				    DO J = 1, M
				      DO ii = I, min(I+T-1, N)
				        A(ii) = A(ii) + B(J)
				      END DO
				    END DO
				  END DO
				  ```
				- > 此时A的cache miss次数是T/b * N/T，B的cache miss次数是M/b*(N/T)，加起来就是(MN)/(bT)+N/b，而tile J层循环得到的cache miss是(MN)* (bT)+M/b，所以对于该循环到底应该tile I层循环还是J层循环更好其实取决于M和N的相当大小。
			- 3. 假设我们同时tile I层循环和J层循环。
				- ```plain text
				  DO I = 1, N, Ti
				    DO J = 1, M, Tj
				      DO ii = I, min(I+Ti-1, N)
				  DO jj = J, min(J+Tj-1, M)
				    A(ii) = A(ii) + B(jj)
				      END DO
				    END DO
				  END DO
				  ```
				- 此时A的cache miss次数应该是N/Ti * Ti/b=N/b, B的cache miss次数应该是Ti/b * M/Ti = M/b, 所以继续提升了cache 命中率。
				- 所以这种对于A和B同时tile的方法需要保证Ti和Tj的A和B同时存在cache中。也就是访问A(Ti)和B(Tj)时A(0)和B(0)同时在cache中。
	- Case 2
		- Naïve loop
			- ```plain text
			  DO J = 1, M
			    DO I = 1, N
			      D(I) = D(I) + B(I, J)
			    END DO
			  END DO
			  ```
			- column major layout
				- B(I, J) 和 B(I+1,J) 在存储中连续
				- > 该循环的cache miss是M*N/b + M*N/b（D和B的miss都是M*N/b）。
		- Loop tiling for I
			- ```plain text
			  DO I = 1, N, T
			    DO J = 1, M
			      DO ii = I, min(I+T-1, N)
			  D(ii) = D(ii) + B(ii, J)
			      END DO
			    END DO
			  END DO
			  ```
			- tiling 之后B的cache miss是T/b * N/T * M = N*M/T, D的cache miss是T/b*N/T=N/b。求和就是N/b + M*N/b。
		- Loop tiling for J
			- ```plain text
			  Do J = 1, M, T
			    DO I = 1, N
			      DO jj = 1, min(J+T-1, M)
			  D(I) = D(I) + B(I, jj)
			      END DO
			    END DO
			  END DO
			  ```
			- 此时B的cache miss是 T*(N/b)*M/T=M*N/b，跟上面一样。D的cache miss是N/b * M/T=M*N/(b*T)。D的cache miss增加了，这是因为在I上没有分块导致。所以当inner most loop中多个变量共享某一个index，可能在对应index分块的效率会更高。
			- 一共的cache miss是 N/b * M/T + T*(N/b)*M/T = M*N/(b*T) + M*N/b。由于M一般远大于T，所以cache miss会更高。
	- Case 3
	  id:: 6221bbeb-d3f4-440b-86c7-f8c6ec72b994
		- Naïve loop
			- ```plain text
			  DO J = 1, N
			    DO I = 1, N
			      A(I, J) = B(J, I)
			    ENDDO
			  ENDDO
			  ```
			- > 无论是否做交换I、J循环（交换是合法的），cache miss都是N*N/b + N*N。
		- Tilling for both I and J
			- T 的选择这里比较tricky，要保证A(ii, T)在cache中时，A(ii, 1)还在cache中。所以此时与A(ii, 1)在同一条cache line上的A(ii + b - 1, 1)也在cache中。也就是cache大小至少需要T*b才能保存A。另外，cache大小至少需要额外的T才能保存B。
			- ```plain text
			  DO I = 1, N, T
			    DO J = 1, N, T
			      DO ii = I, min(I+T-1, N)
			  DO jj = J, min(J+T-1, N)
			    A(ii, jj) = B(jj, ii)
			  ENDDO
			      ENDDO
			    ENDDO
			  ENDDO
			  ```
			- |loop/Cache misses|A|B|
			  |--|--|--|
			  |jj|T|T/b|
			  |ii|T*T/b|T*T/b|
			  |J|N*T/b|N*T/b|
			  |I|N*N/b|N*N/b|
	- Key takeaways:
		- 循环分块是什么？如何做？ #card
		  card-last-interval:: 4
		  card-repeats:: 1
		  card-ease-factor:: 2.6
		  card-next-schedule:: 2023-02-01T15:10:46.890Z
		  card-last-reviewed:: 2023-01-28T15:10:46.891Z
		  card-last-score:: 5
			- 循环分块就是在对应的index循环上划分为多个同样size的tiling
		- 对于不同的循环层分块有什么不同的结果？ #card
		  card-last-interval:: 4
		  card-repeats:: 1
		  card-ease-factor:: 2.6
		  card-next-schedule:: 2023-02-01T15:12:38.938Z
		  card-last-reviewed:: 2023-01-28T15:12:38.938Z
		  card-last-score:: 5
			- 取决于不同循环的访存逻辑。另外，取决于不同循环的循环次数。
		- 如何计算cache miss？ #card
		  card-last-interval:: 4
		  card-repeats:: 1
		  card-ease-factor:: 2.6
		  card-next-schedule:: 2023-02-01T15:14:01.737Z
		  card-last-reviewed:: 2023-01-28T15:14:01.738Z
		  card-last-score:: 5
			- 如上面例子 ((6221bbeb-d3f4-440b-86c7-f8c6ec72b994))
- Loop tiling v2 (reading notes of https://zhuanlan.zhihu.com/p/301905385 )
	- case 3 矩阵乘法三层循环：
		- ```javascript
		  plain text
		  DO J = 1, N
		    DO K = 1, N
		      DO I = 1, N
		   C(I, J) = C(I, J) + A(I, K) * B(K, J)
		      ENDDO
		    ENDDO
		  ENDDO
		  ```
		- I已经被挪到了内存循环，这个跟[wiki: locality of reference](https://en.wikipedia.org/wiki/Locality_of_reference)上的优化一样。
		- > 注：对于矩阵乘法，可能有同学会想到先转置B，然后就不用交换循环了。需要指出的是，转置矩阵本身有不小的开销，而且进行分块以后其实可以和转置达到同样的缓存命中率（一个是temporal locality一个是spatial locality）。而且后面可能B还会以一行一行的方式被访问，也就是说data layout可能不应该改变。于是分块就显得格外重要了。
		- 重复模式分析：
			- 最内侧的I层循环，C(I, J)和A(I, K)的访存顺序时连续的，而B(K, J)是循环无关项。
			- 中间层的K层循环，C(I, J)是重用的。所以可以通过对于I层的切分让C(I..I+T, J)一直保持在cache中。
			- 最外层的J层循环，A(I, K)是重用的。可以通过对于I和K的双重tiling来让A(I..I+Ti, K..K+Tk)一直保存在cache中。
				- ```plain text
				  DO K = 1, N, T
				    DO I = 1, N, S
				      DO J = 1, N
				        DO kk = K, min(N, K+T-1)
				   DO ii = I, min(N, I+S-1)
				     C(ii, J) = C(ii, J) + A(ii, kk) * B(kk, J)
				  ```
				- 对于C，cache miss是N^3/b。对于A，cache miss是N*K/b。对于B，cache miss是N^3/S/b。
			- 可以看到外侧I层循环上B(kk, J)是循环无关项，所以可以通过对与J的tiling，让B在内侧能够重用。
				- ```plain text
				  DO J = 1, N, Tj
				    DO K = 1, N, T
				      DO I = 1, N, S
				        DO jj = J, min(N, J+Tj-1)
				   DO kk = K, min(N, K+T-1)
				     DO ii = I, min(N, I+S-1)
				       C(ii, jj) = C(ii, jj) + A(ii, kk) * B(kk, jj)
				     
				  ```
				- 对于B，此时的cache miss是N*N/b。相对与之前的N^3/S/b有所提升。
			- 但是此时C(ii, jj)仅仅在kk层被重用，在外层的K层的重用并没有被充分利用。
				- ```plain text
				  DO II = 1, N, T2
				    DO J = 1, N, T1
				      DO K = 1, N, T1
				        DO I = II, min(N, II+T2-1), T1
				   DO jj = J, min(N, J+T1-1)
				     DO kk = K, min(N, K+T1-1)
				       DO ii = I, min(N, I+T1-1)
				         C(ii, jj) = C(ii, jj) + A(ii, kk) * B(kk, jj)
				         
				  ```
				- 此时C在L2的cache miss是T1*N*N/b。
				- > 此时ii、kk、jj这三层循环能够fit in L1 cache（tile size是T1），紧接着I层循环虽然fit不了L1但是可以fit in L2 cache（tile size是T2）。于是此时便形成在L2 cache中K层循环对内层I层数据的重用，譬如C(ii, jj)。同理我们还可以做L3缓存的tiling！