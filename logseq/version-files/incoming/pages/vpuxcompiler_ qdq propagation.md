title:: vpuxcompiler: qdq propagation

- #QDQ
- There is an option to [enable the propagation of quantize dequantize in Vpux Compiler](https://github.com/intel-innersource/applications.ai.vpu-accelerators.vpux-plugin/blob/releases/2022/1/src/vpux_compiler/include/vpux/compiler/pipelines.hpp#L206):
- ```c++
  BoolOption enablePropagateQuantDequant{*this, "propagate-quant-dequant",
                                       llvm::cl::desc("Enable Propagate Quantize Dequantize pass"),
                                       llvm::cl::init(false)};
  ```
- `createPropagateQuantizeDequantizePass`
	- before propagate pass:
		- ![image.png](../assets/image_1647332679894_0.png){:height 605, :width 842}
	- after propagate pass:
		- ![image.png](../assets/image_1647332598841_0.png){:height 598, :width 852}