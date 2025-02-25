- Short for:
	- TBU: Translation Buffer Unit
	- TCU: Translation Control Unit
	- IHV: Independent Hardware Vendors
	- WDDM: Windows Display Driver Model
	-
- # Introduction
- ## Block Diagram
	- ![image.png](../assets/image_1714383947287_0.png)
- ## Voltage and Frequency Operating Points
	- Note that VPU IP is a soft IP technology. Can change the voltage and frequency according to the chart here:
	- ![image.png](../assets/image_1714384003898_0.png)
	- DPU to NCE clock ratio is 7:4
- ## Differences from VPU2.7
  collapsed:: true
	- [[vpu2.7 vs vpu4.0]]
	- [[VPU LNL changes on top of MTL]]
	- CPU subsystem
		- 64b RISC-V processor replacement for LeonRT.
	- Host Interface
		- Added a second 64-byte DMA interface.
	- NCE:
		- Removal of LeonNN (inference runtime moves to RISC-V processor).
		- Hardening of SHAVE-NN into a state machine.
		- Automatic barrier/FIFO reloading (reduce synchronisation overhead).
		- New DPU, based on the LSCL architecture
			- Higher frequency scaling
			- Better efficiency for matrix*vector operations and depth-wise convolutions.
		- SHAVE:
			- Increased Activation SHAVE performance (512b vector width).
			- Four sliding windows in the L1 cache, extending the addressable range to 48-bits per SHAVE.
			- Support for 64-bit performance counters.
		- Integrated M2I into the NCE
	- DMA:
		- Bus width increased to 64B.
		- Symmetric bandwidth to DDR and CMX.
		- SW-programmable Pre-fetching.
		- Inline sparsification of weights.
- # Functional Description
- https://docs.intel.com/documents/MovidiusExternal/vpu4/LNL/has/auto/vpu4_toplevel_diagram.vsdx_VPU_Top_LNL_a17fa.zoom.html
	- The VPU IP comprises several individual components grouped into two major subsystems:
		- Host Control (CPU Subsystem, Host Subsystem & Host Interface)
		- Deep Learning (Neural Compute Engine)
	- ### Host Control
		- The functionality of the VPU is exposed to a SoC via a base set of registers (enumerated as a PCIe device or directly memory mapped into the address space of the Host).
		- These registers provide access to control and data path interfaces and reside in the Host Subsystem. All host communications are consumed by the VPU scheduler, a 64-bit RISC-V micro-controller. As well as responding to control messages it manages all the job submission/completion FIFOs that make up the data path of the VPU.
	- ### Deep Learning Accelerators
		- The VPU IP Deep Learning capability is provided by a configurable number of Neural Compute Engine (NCE) Tiles. The NCE Tiles are managed by the VPU Scheduler.
		- Each Tile includes a configurable amount of near-compute SRAM, one Data Processing Unit (DPU) with a configurable number of multiply-accumulates, and up to two DSPs (SHAVE-512) for optimal processing of custom deep learning operations. ==Global barriers and task FIFOs are also available for job synchronization and dispatch.==
	- ![image.png](../assets/image_1714384765528_0.png)
- ## Host Subsystem
	- ![image.png](../assets/image_1714385221854_0.png)
	- #### [[MMIO]]
		- The memory-mapped space is 2GiB and covers the set of registers that are required to configure the VPU. This also provides full visibility into the VPU configuration space and internal RAMs when debug is enabled (assuming suitable firewall permissions).
		- Lunar Lake accesses this port via one of the three PCIe BARs instantiated in the Protocol Bridge. This allows the kernel mode driver to access the VPU registers via the BAR.
		- Note, only 32MiB of the 2GiB MMIO space is enumerated through a PCIe BAR or ACPI.
- ## Host Interface
	- {{embed ((662dc1e7-8fe2-48d0-af78-db878f1f9c04))}}
	- The Host Interface is the upstream interfaces between the VPU and the SoC/buttress. This subsystem is the main data-path interface and consists of:
		- Context Mapping block.
		- ARM MMU-600 for process/context isolation.
	- ![image.png](../assets/image_1714387139311_0.png)
- ### VPU Memory Virtualization
	- [[IOMMU]]
	- With the DirectX10 and subsequent releases, Microsoft divided the driver into two components - a User Mode Driver (UMD) and a Kernel Mode Driver (KMD). They also defined an API from both those Microsoft UMD and KMD components to the HW specific drivers developed by IHVs. As such, the IHV driver would also contain KMD and UMD components.
	- https://learn.microsoft.com/en-us/windows-hardware/drivers/display/iommu-model
	- The IOMMU model defines a single virtual address space that is shared between CPU and the GPU. The single virtual address is managed by the OS memory manager. It also defines the method by which addresses in that shared space can be translated by the HW. Specifically, it introduces a PASID (process address space identifier) that qualifies any virtual address submitted to the GPU and a process by which that virtual address/PASID combination is converted to a physical address by a "compliant IO Memory Management Unit".
	- ![Diagram that shows IOMMU process address space translation in WDDM 2.0.](https://learn.microsoft.com/en-us/windows-hardware/drivers/display/images/iommu-model.1.png)
- ## Ingress Delivery Unit (IDU)
	- The Ingress Delivery Unit reads tensor data and sparsity from the CMX memory and formats this data in a way that allows the Processing Elements (PE) to process it efficiently. The IDU writes the tensor data and sparsity to the Multi Read-port Memory (MRM), which allows multiple PEs to simultaneously process the same tensor data but for different output points.
	- The IDU processes Z-Major Mode formatted data only, where activations are arranged in ZXY format (ZM mode). Channel Major and Depthwise convolutions are performed in ZM Mode.
- ### Z-Major Mode
	- The IDU supports processing of Z major formatted activation data.
	- The IDU supports the following mode for reading activations:
		- Dense Mode:
			- Only activation is read from CMX
		- Sparse Mode:
			- Activation data, **Storage Element Pointers** and activation sparsity are read from CMX
		- *Dense SE Mode
	- The IDU supports the following mode for reading weights
		- Dense mode
			- Weight Pointer and weight data are read from CMX
		- Sparse mode
			- Weight Pointer, weight sparsity and weight data are read from CMX
- ### Feature Set
	- Supports reuse of activations and weights to reduce CMX read bandwidth. There are three modes of activation/weight processing based on how much activations and weights are to be re-cycled by the SCL. The level of reuse drops depending on the tensor size and the number of weight sets.
		- NTHW/NTK = 16/4
			- Activations get re-cycled 4 times
			- Weights get recycled 16 times
		- NTHW/NTK = 8/8
			- Activations get re-cycled 8 times
			- Weights get recycled 8 times
		- NTHW/NTK = 4/16
			- Activations get re-cycled 16 times
			- Weights get recycled 4 times
	- Supports the following MPE grid assignments:
		- 16x16 (for NTHW/NTK = 16/4)
		- 16x8 (for NTHW/NTK = 8/8)
		- 8x8 (for NTHW/NTK = 4/16)
	- IDU block diagram
		- ![image.png](../assets/image_1654155515724_0.png)
			- assets:///C%3A/Users/xuqian/OneDrive%20-%20Intel%20Corporation/Documents/logseq/assets/image_1654155515724_0.png
		- Z-major controller
- ## Sparse Cell (SCL)
-
	- ![image.png](../assets/image_1654154607254_0.png)
	- The SCL consists of a 4x4 arrays of MPEs which are connected to 4 weight MRM and 4 activation MRMs.
	- To allow reuse of activations and weights and to boost the number of operations per byte fetched from memory, the SCL adds an **Accumulator Storage Register File (RF)** to store partial accumulations. The RF is shared between 4 MPEs in a row and allows the storage of up to 64 accumulator contexts per MPE. This allows each SCL to generate up to 1024 output points at a time (4x4x64). The 4 accumulator RFs must share a single bus between the SCL and the PPEs and this requires an RF Output MUX block that is responsible for sequencing the data that is produced from each RF and then pushed to the PPEs.
	- ![image.png](../assets/image_1654156475796_0.png)
	- a 4x4 Sparse Cell Array (SCA) is created as shown in Figure. The SCA requires two additional blocks in addition to the SCL - an activation MRM MUX and a weight MRM MUX.
	- ==Each row of activation MRM FEs contains the same activation data. Similarly, each column of weight MRM FEs contains the same weight data.==
	- It is necessary to coordinate the read requests from each FE within a row/column and present a single read request to the IDU. Similarly, when the IDU returns data, the data needs to be distributed to the requesting FEs. This role is performed by the MRM MUXs which are responsible for aggregating requests and routing read data based on the current mode of operation of the DPU.
	- ![image.png](../assets/image_1654156590807_0.png)
	- The activation and weight readers within the IDU fetch the data and sparsity over CMX and buffer the data within the IDU. The Sparse Cell Array make requests to the IDU via the MRM MUX and performs the convolution operations when the data is received. Once the Sparse Cell Array reaches the end of the tensor in the z-dimension (the input channel length C), the 64 RFs within the Sparse Cell will then have up to 16,384 output activations available. The 16 RF MUX blocks within the Sparse Cell array sequence the data out of the 64 RFs and push this data to a bank of 64 PPEs. Once the PPEs have processed the activations, these are passed to the ODU where the output tensor is built up and written out over CMX.
	- ### Feature overview
		- Data Reuse
			- 64 Contexts per MPE
			- 4x-16x reuse on Activations
			- 4x-16x reuse on Weights
			- Up to 16,384 Output points generated at a time
			- 4KB Partial Accumulator storage per Sparse Cell (64KB per SCL Array)
	- ### Interfaces
		- IDU interface
			-
-