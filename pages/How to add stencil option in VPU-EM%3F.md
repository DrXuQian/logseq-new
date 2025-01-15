title:: How to add stencil option in VPU-EM?
- json output:
	- stencil:
		- 8_8
		- 16_4
		- 4_16
- From Wangyi:
	- 如果NB 没有给， conv default会用 16_4,  depthwise 和 pooling， elementwise是写死的。 Depthwise 用的是8_8 (但实际用的是8_4），max_pool 实际用的是16_1。
	- 目前只对convolution有影响。
-