### Level 0 compilation
- [[workload management]]
- ### VPU-IP hardware exposure
	- VPU 在host端表现出来是两个engine，一个是copy engine，一个是NCE engine。然后我们通过不同的[[command queue]]来提交不同的task到VPU。
	- 可以看到OS其实管的就是提交job到queue里面，然后JSM会去处理后面的分发。
	- ![image.png](../assets/image_1706083543607_0.png)
- [[LeonRT]] 干了啥？
	- LeonRT is a MCU that runs firmware.
	- 管理workload：
		- Host software submits work through one of the 256 doorbells and this invokes the LeonRT core to perform the scheduling operation on the appropriate engine.
		- Host 软件先ring doorbell，然后LeroRT的core被interupt，然后把对应的workload放到对应的engine上运行。
		- 调度的过程包括判断下一个要运行的workload，提交workload到LeonNN，然后监测work是否完成。
	- [[Job scheduling]]：
		- 管理不同的job的priority并调度。
	- [[Context]]管理：
		- LeonRT同样也会管理context。
- ### [[Job Scheduling]]
	- On level 0 interface, job会放到一个command queue里面被提交到VPU
	- 然后job的描述符会被放到一个command list里面。
	- #### OS/KMD Based Scheduling
		- 如图所示，在OS/KMD或者windows的scheudling中，LeonRT能看到两个submission queue，然后去调度给对应的硬件。
		- ![image.png](../assets/image_1706084803512_0.png)
	- #### Linux KMD Scheduling
		- Linux 里面KMD会处理scheduling的工作。（QOS based scheduling）
	- #### Windows HW scheduling flow
		- ![image.png](../assets/image_1706084971377_0.png)
- ### [[Job Submission]]
	- What is the difference bw OS/KMD based scheduling and KMD based scheduling?
	  collapsed:: true
		- 一个调度是OS做，一个是[[LeonRT]]做。
		- The difference between HW scheduling and OS based schedule is the component responsible for managing the schedule. In OS based scheduling the **DXGKRNL performs the scheduling algorithm**, in HW based scheduling, the **Job Scheduler in the LeonRT FW will perform the scheduling** algorithm. In the case of OS/KMD based scheduling the KMD will ring the doorbell to submit a new job after being called by the DXGKRNL based scheduler. In the case of HW scheduling the KMD assigns some doorbells to each UMD for requesting execution.
	- ![image.png](../assets/image_1705988358532_0.png){:height 728, :width 518}
	- Workload submission flow:
	  collapsed:: true
		- [[MMIO]]
		  collapsed:: true
			- MMIO（Memory-Mapped Input/Output）是一种在计算机系统中处理输入/输出（I/O）操作的方法。通过MMIO，设备的寄存器或状态被映射到主内存的地址空间中，这样处理器就可以使用普通的内存访问指令来读写这些设备寄存器，而不是使用专门的I/O指令。
		- [[doorbells]]
		  collapsed:: true
			- {{embed ((65b1b066-80f1-4e93-a24c-e74e39306777))}}
			- 在计算机架构领域中，提到 "doorbells" 时，它指的是一种同步机制，用于在多核处理器或多个计算单元之间通信。这种机制允许一个处理单元通知另一个处理单元有数据或任务需要处理，从而实现处理单元之间的协调和数据传递。
		- {{embed ((66377d50-c0aa-402a-abdc-f2af5be8ba1a))}}
		- Submission flow:
		  collapsed:: true
			- ![image.png](../assets/image_1705991274477_0.png)
			- ![image.png](../assets/image_1705991250176_0.png)
	- #### Building a [[Command List]]
		- ![image.png](../assets/image_1715068170313_0.png)
	- #### Command Hierarchy
		- There will be two levels of command hierarchy. At the highest level the framework or application will build a [[command list]]. The commands that are submitted include 3 types:
		  collapsed:: true
			- Compute commands: will execute on DPU or SHAVE
			- Copy commands: will execute on the DMA engine
			- Barriers/events: will be managed by the job scheduler
			- these barriers should not be confused with HW barriers supported within the VPU
		- The next level of command will be either an engine specific command list/schedule such as the graph schedule to run a neural network or commands for the DMA engine for a copy job. Or it will be dependency type commands such as barriers, fences, etc. The diagram below shows a mapping from these high level command types to low level commands to be sent to the particular engine
		- ![image.png](../assets/image_1715068425785_0.png)
	- #### Scheduling and Executing a [[Command List]]
		- ![image.png](../assets/image_1715068542629_0.png)
	- #### [[Compute engine]] flow
		- ![image.png](../assets/image_1715068627328_0.png)
	- #### [[Copy engine]] flow
		- Note that the copy of weights/inputs and output are from DDR to DDR
		- In order to initialize an inference job a DMA command will be given to DMA including weights, activations, compiled SHAVE code, graph execution description, etc. It will be ==copied from a CPU accessible to non-CPU accessible region in case of a default heap,== although if a custom heap is used it could still be in a CPU accessible region.
		- ![image.png](../assets/image_1715068726406_0.png){:height 648, :width 911}
		- General flow of copy engine
			- ![image.png](../assets/image_1715068825914_0.png)
		- DMA engine scheduling
			- ![image.png](../assets/image_1715068885799_0.png)
		- Copy job submission
			- ![image.png](../assets/image_1715068932846_0.png)
	- #### Job Completion
		- notify the KMD with IPC FIFO
		- ![image.png](../assets/image_1715069095737_0.png)
	- #### Graph handling for [[job submission]]
	  collapsed:: true
		- ![image.png](../assets/image_1705991523488_0.png)
	- #### DMA Engine Scheduling
	  id:: 65acce80-ccec-45a0-afcc-f7deeec17541
		- This is for VPU2P7
		- 总共有9个[[link agent]]，每一个link agent我们都可以提交[[link list]]，具体的功能如下。值得注意的是，WL descriptor fetch, fifo fill 以及barrier config write都是通过DMA来实现的。
		- 这里的usage有问题。。。
		- ![image.png](../assets/image_1705823866608_0.png)
	- #### DMA Job Completion
		- There will be ==two== mechanisms for job completion, one for compute jobs and one for copy jobs. For compute jobs, the execution will be barrier driven to signal job completion. For copy jobs, copy engine runtime will register to receive an interrupt upon completion. In order to receive an interrupt upon completion, the DMA-Task field for the job should be programmed.
- ### DMA Use Case flows [[VPUIP DMA]] [[DMA in VPU]] [[Link Agents]]
	- The compiler needs to prepare the DMA descriptor for both data fetch as well as "instruction" fetch, where instruction fetch refers to WL descriptors for DPU and SHAVE.
	- The following use cases are covered here:
		- Copy weights and activations from DDR -> CMX
		- Copy activations from CMX -> DDR
		- Fetch WL descriptors from DDR into CMX
		- Fetch FIFO entries from DDR and program into FIFO registers
			- legacy flow could copy FIFO entries from CMX to FIFO registers
		- perform reshape operations
		- convert from tile to planar format (use case scenario not yet defined)
		- perform scatter/gather operations (operator mapping not yet defined)
		- perform data conversion (such as FP32 to FP16, etc)
		- Erase memory
		- page table pre-fetch into MMU TLB
- ### page table translation and impact on performance
	- [[MMU]] contains a cache for page table translation to support on the fly translations of [[PIOVA]] to [[IOVA]] addresses
	- If translation is not in the TLB cache, the translation need to fetch from DDR which will incur 100~ cycle DDR round trip latency.
	- 可能会在下面的场景中出现：
		- DMA fetch of weights/activations
			- ==spilled activation fetch may not suffer a page table miss since those activations were likely just written out to DDR==
		- DMA write of activations (less sensitive to latency)
		- SHAVE read of data via either DMA or L2$
			- same note as above applies with respect to reading spilled activation values
		- code fetch
	- The compiler is expected to typically pre-fetch weights and activations while compute is happening in parallel and therefore TLB translation fetch may not impact performance depending on whether the operation is data bound or compute bound. But If a layer is purely compute bound the layer can be delayed by the any stalls due to translation fetches. Additionally anytime that SHAVE is trying to access data either through the L2$ or DMA it will directly impact the kernel performance.
	- #### Hardware assisted page table pre-fetch
		- VPU2.7 introduces HW support in the DMA engine for a pre-fetch engine. The job of the pre-fetch engine is to pre-fetch the translations.
		- The prefetcher is HW driven. It snoops the addresses coming out of the DMA engine and creates ==dummy reads to trigger an address translation service for address values offset to the snooped address.==
	- #### Compiler assisted page table pre-fetch
		- interesting, compiler can create a very small DMA task to pre-fetch the page table entries.
- ### DONE [[Preemption]]
  :LOGBOOK:
  CLOCK: [2024-05-07 Tue 16:15:00]--[2024-05-07 Tue 16:15:03] =>  00:00:03
  :END:
	- [Preemption_NPU4-NPU5-NPU6](https://intel.sharepoint.com/sites/VPUIPArchitecture/_layouts/15/stream.aspx?id=%2Fsites%2FVPUIPArchitecture%2FShared%20Documents%2FVPU%20Client%20AI%20Arch%20Presentation%2FEnd%2Dto%2DEnd%20System%20Architecture%2FPreemption%2FPreemption%5FNPU4%2DNPU5%2DNPU6%2Emp4&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2E21031d44%2Dcba2%2D4ac1%2D88ee%2D68d93a8d9e0e)
-