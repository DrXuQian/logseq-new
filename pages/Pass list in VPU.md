- ```
  ### `-wrap-vpu-ops-in-ncecluster-tiling`: This pass wraps vpu operations that should be executed across multiple clusters in NCEClusterTiling operations
  This pass builds an IR in order to represent multi-cluster compilation. It performs a number of functions.
  1) It creates variations of distributed tensors depending on the multi-cluster strategy of the layer.
  2) It creates DMA operations DDR->CMX and wraps the DMAs in NCEClusterTiling.
  3) It wraps hardware executable operations in NCEClusterTiling.
  ```
- IE passes:
	- `src/vpux_compiler/docs/chapters/generated/dialect/IE/_passes.md`
- VPU passes:
	- `src/vpux_compiler/docs/chapters/generated/dialect/VPU/_passes.md`
- VPUIP passes:
	- `src/vpux_compiler/docs/chapters/generated/dialect/VPUIP/_passes.md`