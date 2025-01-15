---
title: Im2col
---

- Im2col + BLAS
	 - Im2col
		 - ![Im2col GEMM converted from the convolution in Fig. 1. The red boxed... |  Download Scientific Diagram](https://www.researchgate.net/publication/332186100/figure/fig2/AS:743806039244803@1554348587949/Im2col-GEMM-converted-from-the-convolution-in-Fig-1-The-red-boxed-data-show-duplicated.png)
			 - 将输入由 IH×IW×IC 根据卷积的计算特性展开成 (OH×OW)×(KH×KW×IC) 形状的二维矩阵。显然，转换后使用的内存空间相比原始输入多约 KH∗KW−1 倍。

			 - 权重的形状一般为 OC×KH×KW×IC 四维张量，可以将其直接作为形状为 (OC)×(KH×KW×IC) 的二维矩阵处理。

			 - 对于准备好的两个二维矩阵，将 (KH×KW×IC) 作为累加求和的维度，运行矩阵乘可以得到输出矩阵 (OH×OW)×(OC)。这一过程可以直接使用各种优化过的 [BLAS](https://en.wikipedia.org/wiki/Basic_Linear_Algebra_Subprograms)（Basic Linear Algebra Subprograms）库来完成。

			 - 输出矩阵 (OH×OW)×(OC) 在内存布局视角即为预期的输出张量 OH×OW×OC 。

		 - Im2col wouldn't change the memory layout for 1x1 convolutions, else it would consume more memory for input features.

- ![通用矩阵乘（GEMM）优化与卷积计算](https://pic3.zhimg.com/v2-148b05ce0e38be18903896e8accf2690_1440w.jpg?source=172ae18b)
id:: f70160af-958d-45d3-8652-494550047071
