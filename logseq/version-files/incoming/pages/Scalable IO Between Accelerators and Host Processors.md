- https://www.intel.com/content/www/us/en/developer/articles/technical/scalable-io-between-accelerators-host-processors.html
- ### Process Address Space ID (PASID)
	- PASID is an optional feature that enables sharing of a single Endpoint device across multiple processes while providing each process a complete 64-bit virtual address space. In practice, this feature adds support for a TLP prefix that contains a 20-bit address space that can be added to memory transaction TLPs.
	- IOMMU会根据PASID来加载不同的page table.
- 加速器硬件用传统的IO device的软硬件设计所面临的问题：
	- Offload acceleration hardware and software that evolved from traditional I/O controller designs suffer from many challenges. These include work submission overheads, memory management complexity, work completion synchronization overheads, and sharing accelerators among applications or tenants.
- ### Work Submission Overheads
	- > Traditionally, when an application submits work to an accelerator, the request is routed to a kernel-mode device driver that abstracts the interface between the software and hardware of the underlying accelerator device. The latencies and overheads associated with kernel I/O stacks can cause bottlenecks for high-performance, streaming accelerators.
	- 上下文切换的开销：内核模式提交请求涉及到用户态切换到内核态
	- 数据复制：用户空间和内核空间直接需要数据复制
- ### Memory Management Complexity Overheads
	- > Traditional accelerator designs require pinned memory to share data between an application and a hardware accelerator. Also, an accelerator's view of memory (accessed via direct memory access) is significantly different from an application's view, often requiring the underlying software stack to bridge the two views through costly memory pinning and unpinning and DMA map and unmap operations.
	- pinned memory:
		- 固定内存是指操作系统不会将其交换到磁盘的内存区域。可以保证数据的物理地址不变，这对于DMA于硬件通信比较重要。
		- 通常当一个应用程序访问一个虚拟地址，并且这个地址已经被操作系统交换到磁盘上时，会触发一个page fault，然后会经过如下的步骤：
			- 当访问一个已经swap的内存页面，MMU无法找到对应的entry，所以触发page fault，缺页中断，然后转移到操作系统。
			  logseq.order-list-type:: number
			- 操作系统的内存管理系统接管，处理这个页错误，查看虚拟地址的有效性并且程序是否有权限访问这个虚拟地址。
			  logseq.order-list-type:: number
			- 操作系统，从物理内存中找到一个空闲页面或者通过LRU回收一个页，然后从磁盘上的交换区把数据读回到这个新的物理内存页。
			  logseq.order-list-type:: number
			- 操作系统更新内存页表，虚拟地址映射到新的物理地址。
			  logseq.order-list-type:: number
			- 然后重新执行刚才的页错误的指令。
			  logseq.order-list-type:: number
	- 特殊情况，hardware accelerator和pinned memory
		- 当硬件加速器访问一个虚拟地址，如果对应的虚拟地址被swap到了磁盘上，无法处理这样的page fault，并且访问速度会很慢，对于硬件加速器，吞吐量非常重要，所以要避免这种场景。
- ### Work Completion Synchronization Overheads
	- Existing software stacks for accelerators rely on either interrupt-driven processing, busy-polling of completion status registers, or descriptors in memory. Interrupt processing architecture in operating system kernels imposes overhead with context switching and deferred processing that can reduce system performance. On the other hand, busy polling consumes more power, reduces the number of CPU cycles available to other applications, and can negatively impact scaling.
	- 中断或者轮询来进行设备完成任务的通知
- ### Accelerator Shareability
	- 在多个应用程序，container或者VM上共享硬件加速器的问题
	- 单根IO虚拟化 SR-IOV
		- 允许device driver和设备间绕过虚拟机管理程序进行通信。看起来就像是多个独立的设备，分配到了不同的虚拟机。
- ### Shared Virtual Memory
	- 允许加速器使用和CPU一样的虚拟地址
		- 这样加速器可以直接访问应用程序的地址空间，从而简化了数据处理流程，减少了数据拷贝的需要
	- Enabling the accelerator to access unpinned memory and to recover from the I/O page-faults
		- 可以处理页错误
	- ![](https://www.intel.com/content/dam/developer/articles/technical/scalable-io-between-accelerators-host-processors/scalable-io-whitepaper-fig2.png)