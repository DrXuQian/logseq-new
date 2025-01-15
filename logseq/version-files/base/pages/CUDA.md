- https://www.nvidia.com/content/PDF/fermi_white_papers/NVIDIA_Fermi_Compute_Architecture_Whitepaper.pdf
- https://scs.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=c802ccf7-c8bc-4feb-83da-19ef17f1ee90
- ### Thread/Block/Grid
  collapsed:: true
	- [[CUDA 线程模型]]
	- ![image.png](../assets/image_1705905186675_0.png)
	- 每个thread执行kernel的一个instance，并且有一个unique的thread id，同时有独立的程序计数器，寄存器，以及私有的memory空间。
	- 一个block对应了一组能够并发的thread(最多1024个)，这些thread之间必须没有data dependency，也就是可以乱序执行。并且一组block的thread可以同步内存。因为每一个block对应到硬件应该是一个SM，每个SM内部同时运行的thread应该共享L1 cache，在hopper架构下面应该是192KB的空间。
	- 一个Grid对应了一组运行同一个kernel的thread block。都是从主存中读取数据，并且写入主存。同时在有数据依赖的不同kernel之间同步数据。
	- CUDA的编程模型中，每个thread有自己的独立私有空间，可以用于register spill，函数调用。。每个block有block内部的共享空间，用于thread之间的数据交互。Grid之间通过global memory交互数据。
	- **Example**
		- 创建6个block，每个block有12个thread。
		  collapsed:: true
			- ![image.png](../assets/image_1705910716614_0.png){:height 467, :width 791}
		- Code:
		  collapsed:: true
			- Each thread is going to run the cuda kernel here，所以这个程序运行的时候会有72个thread，每个thread的指令都是下面的图上的指令。
			- instruction就是load A，load B，然后accumulate，然后save to C。
			- ![image.png](../assets/image_1705910832836_0.png){:height 551, :width 766}
		- Execution Model:
			- Need to cudaMalloc first and cudaMemcpy to have communication bw host and device memory.
			- ![image.png](../assets/image_1705911111355_0.png){:height 495, :width 637}
- ### Map to hardware
  collapsed:: true
	- GPU 执行一个或者多个kernel grid。一个SM执行一个或者多个线程块。在SM内部，CUDA core/tensor core/SFU 等单元用于计算具体的thread。
	- SM一次执行32个thread，叫做一个warp。
- ### Streaming multiprocessors
  collapsed:: true
	- [[SM]]
	- ![image.png](../assets/image_1705905804338_0.png){:height 920, :width 792}
	  id:: 65b1b063-97ef-4c3b-a1e2-82c9b5a8f502
	- #### Dual [[Warp]] Scheduler
	  id:: 65b1b063-f9c2-432b-9de9-7dc69e9c08b5
	  collapsed:: true
		- Fermi's dual warp scheduler selects two warps, and issues one instruction from each warp to a group of sixteen cores, sixteen load/store units, or four SFUs.
		- ![image.png](../assets/image_1705906817472_0.png)
		- 每一个SM里面有两个warp scheduler，还有两个instruction dispatch unit。可以允许两个warp同时执行。
		- 每个warp对应了32个并行的thread
	- Warp in example:
		- GTX 980: Maxwell
		- ==Ability to keep on the chip 64 warps==
		- That corresponds to 64 simultaneous instruction streams, each of the stream is going to run 32 width instruction SIMD.
		- Every clock, I have the ability to select 4 of the 64 and run them. And each warp scheduler have 2 instruction dispatcher so every clock, we have 8 32-width SIMD that can run on ALUs. This corresponds to the 32 threads belong to one warp.
		- The key thing is that warp execution contexts is 64, which is way larger than warp selector which is 4. This is because we can switch context when the data is not ready for some threads.
		- ![image.png](../assets/image_1705913212424_0.png){:height 462, :width 702}
		- Groups of 32 CUDA threads share an instruction stream, and the groups are called warps.
		- 由于conv这个程序每个block有128个thread，可以用4个warp，然后变成4个instruction stream执行。
		- ![image.png](../assets/image_1705913589722_0.png){:height 507, :width 707}
	- **Memory sharing bw threads/grid/blocks:**
	  collapsed:: true
		- ![image.png](../assets/image_1705911237060_0.png){:height 560, :width 724}
		- Grid share global memory, block share l1 memory, thread have private memory.
	- **Example**:
	  collapsed:: true
		- 对于一个1D的convolution，每个输出对应了三个输入。
		- 这里用了1024*1024个thread，每个block有128个thread，也就是有8K个block。每个thread对应计算一个输出的点，然后每个thread的内部计算就是红框对应的代码，每个kernel需要load 三次，加三次，然后写三次。
		- ![image.png](../assets/image_1705911395302_0.png){:height 538, :width 711}
		- 优化的版本，增加了shared memory：
		- 创建了一个1030大小的share memory，每一个thread，从global memory中取一个数据，并且存到shared memory里面，然后所有的thread sync一下。这样的话，后面的红框对应的代码就不需要每次都从global memory读取3个数据，而只需要从local memory里面去取就行了，相当于减少了2//3的数据读取。由于local memory非常快，相对于global memory可以认为这个延迟忽略不计。
		- 值得注意的是其中有一个__syncthreads，是block的barrier。
		- 这里可以从block的执行角度，每个SM上运行了一个130那么大的1D convolution。但是这里有8K个block。
		- ![image.png](../assets/image_1705911580405_0.png){:height 563, :width 767}
		- CUDA中软件映射到硬件，但又不完全是，如果写的特别固定，比如某个block对应了某个SM，那么对于扩展性就是灾难。
		- 在编译的过程中，我们可以知道这个程序需要每个block有128个thread，然后520bytes的shared memory。
		  collapsed:: true
			- ![image.png](../assets/image_1705912455908_0.png){:height 216, :width 273}
		- GPU如何分配thread
		  collapsed:: true
			- 如图所示，thread block scheduler在kernel launch之后用于分配block，动态根据当前的资源情况分配到不同的SM。
			- ![image.png](../assets/image_1705912497301_0.png){:height 450, :width 601}
- ### Context switching
  collapsed:: true
	- 对于某一个[[warp]]，包含了32个thread，如果其中一个或者多个operands还没有ready，也就是还没有从主存中获取。那么我们会有一个context switching的过程。这个过程中，我们会把当前的warp替换成另外一个warp，当前的warp的数据还是会保留在register file里面，这样的话再后面重新执行这个warp的时候能够快速回复执行。
	- How many threads can there be in a GPU?
		- 16个core，每个core上有128个ALU，所以是4.6TFlops
		- 16个core，每个上面有64个warp execution contexts，也就是总共1024个warp，每个warp对应了32个thread，也就是32个instruction stream。总共32768个thread per chip。
		- ![image.png](../assets/image_1705913927043_0.png){:height 470, :width 718}
	- **Example:**
		- Step1:
			- ![image.png](../assets/image_1705914681556_0.png){:height 462, :width 727}
		- Step2:
			- ![image.png](../assets/image_1705914705180_0.png){:height 443, :width 720}
		- Step3:
			- ![image.png](../assets/image_1705914496151_0.png){:height 536, :width 725}
		- Step4:
			- ![image.png](../assets/image_1705914532450_0.png){:height 468, :width 732}
		- Step5:
			- ![image.png](../assets/image_1705914559488_0.png){:height 464, :width 735}
		- Step6:
			- ![image.png](../assets/image_1705914540250_0.png){:height 451, :width 735}
		- Step7:
			- ![image.png](../assets/image_1705914644691_0.png){:height 452, :width 738}
-