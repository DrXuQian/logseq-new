- [5/6 11:13 AM] Crews, Darren S
  so we have this section in the SAS: https://docs.intel.com/documents/MovidiusExternal/vpu27/MTL/SW/VPU27SAS.html#compilation-flow, but it was written before VPUx really started, so I think it should be updated to align with this material: https://docs.intel.com/documents/MovidiusExternal/vpu2/Common/SW/HLD/external/VPUX_NN_Compiler.html#frontend
- ## sparsification
	- ### Section:
		- ![image.png](../assets/image_1654492365675_0.png)
	- ### Contexts:
		- [5/20/2022 1:47 AM] Crews, Darren S
		  I looked through what you added in the SAS for sparsity. I would recommend adding some additional information from this document: https://docs.intel.com/documents/MovidiusInternal/vpu27/common/PnP/VPUPreSiPnP.html#activation-sparsity
		- for example this is quite interesting. I think we should list out the relevant guidance such as:
			- sparse workloads must have an output channel size which is a power of 2 (minimum 16), e.g 16,32,64,128,256 etc
			- To benefit from the compression aspect of sparsity, a minimum of 12.5% sparsity is needed for 8-bit weights or activations. However, since the CMX minimum read granularity is 128 bits or 16 bytes, the effective amount of sparsity required could be significantly larger to ensure the CMX traffic does not grow between dense and sparse modes.
			- https://docs.intel.com/documents/MovidiusInternal/vpu27/common/PnP/VPUPreSiPnP.html#a-layer-unsuitable-for-weight-sparsity-compression
			- For ZM ops with Z <= 32 & kernel size = 1, CMX BW can be worse for sparse than dense
			- An output channel size of multiples of 128 would be optimal so the sparse map data would occupy the full 16 bytes, and therefore requires a single write. An output channel size of 16 (the minimum allowed) would result in the worst relative performance since it would demonstrate the lowest effective BW given that only 1/8th of the sparsity map data written at any point would be useful.
			- A basic rule of thumb applied (in the absence of a more complete cost model) was to suggest any workload with an output channel size < 64 would be considered for switching to dense operation.
			- maybe more. Maybe we could setup a sync with Kyle Mcadoo to determine the relevant summary data that we can put.
		- I got some more clarification on sparsity limitations:
		- [7:26 AM] Hanrahan, Niall
		  For sparse mode, on 4P0 we support non power of 2 channel size and splits in the channel size which are not a power of 2 (i.e. can do SOK with any number of channels which are multiple of 16). On 2P7 we support output channel size which is not a power of 2 as long as there are no splits - if there are splits then there is a constraint that the splits must be a power of 2 in size (so say you have Z=320 and you wany to split across 2 DPUs as 2 WLs, on 2P7 you would have to do 256/256 so would have to pad out to 512, but on 4P0 you can do 2 WLs each of which has Z=160)
		- 6/1/2022 8:21 AM
		  For weight sparsity in SAS, are we supposed to mention the cases the weight sparsity does not bring benefit?
		  yeah definitely
	- ### Sparsity Enabled Acceleration
		- Output channel size and to a lesser degree the input channel size can have a major affect on the ability of sparsity to accelerate performance.
		- ### Activation Sparsity
			- Why we have granularity for reads and writes of 128 bits for CMX?
				- During execution, data is being read and written into the 32 CMX (per slice) cuts by the DMA, IDU and ODU. This data should be swizzled to maximize the bandwidth across the 32 cuts and reduce stalling which occurs when multiple read/write agents request access to the same cut. **CMX alignment still enforces a minimum granularity for reads and writes of 128 bits, or 16 bytes.** This is the case for all CMX accesses, including sparsity map writes by the ODU.
				- In the case of u8/i8 each sparsity map byte contains the sparse information for 8 weights or activations, so ==a single CMX sparsity map read would represent the sparse information for 16 * 8 = 128 bytes of data==. While adopting a z-major layout, the DPU is processing the tensor in chunks sized in terms of its output channel size. ==An output channel size of multiples of 128 would be optimal so the sparse map data would occupy the full 16 bytes, and therefore requires a single write. An output channel size of 16 (the minimum allowed) would result in the worst relative performance since it would demonstrate the lowest effective BW given that only 1/8th of the sparsity map data written at any point would be useful.==
				- A basic rule of thumb applied (in the absence of a more complete cost model) was to suggest any workload with an output channel size ==< 64 would be considered for switching to dense operation.==