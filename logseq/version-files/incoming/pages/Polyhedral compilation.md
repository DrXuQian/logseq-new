- ![多面体编译理论与深度学习实践.pdf](../assets/多面体编译理论与深度学习实践_1693892177829_0.pdf)
- # Chapter1: 体系结构发展对编译技术的影响
- ## 1.1 面向经典体系结构的性能优化
- ### 1.1.1 并行性发掘
	- data level parallelism: 同时操作多个数据
	- task level parallelism: 多个并行而且独立的任务处理多个数据
	- 指令级并行
	  logseq.order-list-type:: number
		- 单个处理器核心中，同时执行多条指令
		  logseq.order-list-type:: number
			- Pipeline
			  logseq.order-list-type:: number
			- Multiple issue
			  logseq.order-list-type:: number
			- Simultaneous multithreading
			  logseq.order-list-type:: number
			- Very long instruction word
			  logseq.order-list-type:: number
	- 单指令多数据并行
	  logseq.order-list-type:: number
		- SIMD
		  logseq.order-list-type:: number
			- SSE/AVX512 for Intel CPU
			  logseq.order-list-type:: number
			- NEON for ARM
			  logseq.order-list-type:: number
	- 线程级并行
	  logseq.order-list-type:: number
		- 单个芯片上集成多个处理器核心
		  logseq.order-list-type:: number
	- 请求级并行
	  logseq.order-list-type:: number
		-
- ### 1.1.2 存储层次结构
	- Local memory:
		- L1 cache
	- Shared memory by multi-core:
		- L2 cache
	- [[Scratchpad memory]]
- ### 1.1.3 领域专用架构
	- domain specific architecture
- ## 1.2 编译器面临的挑战
- 流水线的结构冲突，数据依赖和控制依赖会在处理器流水线中引入空泡(buble)。
- Loop tiling and loop fusion
- ## 1.3 循环优化的数学抽象
- ### 1.3.1 多面体模型的基本概念
-