---
title: Paper: WDSR
---

- Metadata
	 - [[Class]]: Computer Vision

	 - [[Topic]]: SISR

	 - [[Lecturer]]: Yu, Jiahui

	 - [[Date]]: 2018 Dec

	 - [[Keywords]]: [[WDSR]] [[SISR]]

- Recall Questions
	 - {{embed  ((cdedd3c9-506d-40ee-83e5-09b87bf3240e))}}

	 - What is the difference between WDSR and EDSR?
		 - ((7b8f9e1e-c7fe-40ed-be3b-260f1e040da0))

		 - ((b3255d35-5ca8-43fc-8114-71bfaeba3afd))

	 - Why use wider features before Relu?
		 - ((e84404a8-16a8-4ccc-bbc6-271d34bf7534))

		 - ((8c09e72b-b9f2-4367-babb-8fba941badd4))

	 - {{embed  ((37a4abe2-3490-42eb-9621-7f7790fc1cca))}}
		 - ((be24b4bb-f74e-465d-946e-8cfdeffc978d))

	 - Why batch normalization perform badly with SISR?
		 - ((6b19288d-aa94-4e23-9ddd-b8ad77924fb1))

		 - ((2fbd1fca-03c2-4a90-9da6-02d3a66f2f08))

	 - Why can WDSR use higher learning rate?
		 - ((4336bcdc-ce88-484a-88b2-7d83fc7365cd))

	 - What is PixelShuffle more used in SISR than Deconv?
		 - {{embed  ((acd9ed01-c439-46df-aaa6-8b4c12ff4e5c))}}

	 - How can we keep the computation the same while having wider activations?
		 - {{embed  ((6e96b4d6-8cd9-4ba1-9293-b30dcc853e51))}}

	 - What did WDSR-B used to reduce computation?
		 - ((debf35fc-f9ab-451d-8466-76d98b46afd6))

	 - What is the highlight of WDSR?
		 - ((53d7bfe9-22a8-4e8c-830e-0629c00c10d8))

	 - What is the loss function of WDSR?
		 - L1 loss

- Notes
	 - **Extracted Annotations (2022/2/9 下午10:07:10)**

	 - "with same parameters and computational budgets, models with wider features before ReLU activation have significantly better performance for single image super-resolution (SISR)." ([Yu et al 2018:1](zotero://open-pdf/library/items/XBS8CESG?page=1))
		 - __Wider features meaning increasing channels. o(H * C* C) parameters according to EDSR. So this increase the representation power by adding more weights while keeping the complexity. What is the difference between WDSR and EDSR? ([note on p.1](zotero://open-pdf/library/items/XBS8CESG?page=1))__

	 - "training with weight normalization leads to better accuracy for deep super-resolution networks." ([Yu et al 2018:1](zotero://open-pdf/library/items/XBS8CESG?page=1))
		 - What is weight normalization? ([note on p.1](zotero://open-pdf/library/items/XBS8CESG?page=1))
id:: cdedd3c9-506d-40ee-83e5-09b87bf3240e

	 - "The increasing of depth brings benefits to representation power [2, 5, 18, 28] but meanwhile under-use the feature information from shallow layers (usually represent low-level features). To address this issue, methods including SRDenseNet [36], RDN [42], MemNet [33] introduce various skip connections and concatenation operations between shallow layers and deep layers, formalizing holistic structures for image super-resolution" ([Yu et al 2018:1](zotero://open-pdf/library/items/XBS8CESG?page=1))
		 - __Interesting point. Increasing depth would mean that network underuse the information from shallow layers. Densenet like networks provide solution by adding skip connection and concat/add features from different stages. ([note on p.1](zotero://open-pdf/library/items/XBS8CESG?page=1))__

	 - "non-linear ReLUs impede information flow from shallow layers to deeper ones" ([Yu et al 2018:1](zotero://open-pdf/library/items/XBS8CESG?page=1))
		 - __非线性的activation阻碍了shallow feature的向后传播 ([note on p.1](zotero://open-pdf/library/items/XBS8CESG?page=1))__
id:: e84404a8-16a8-4ccc-bbc6-271d34bf7534

	 - "Compared with vanilla residual blocks used in EDSR [19], we introduce WDSR-A which has a slim identity mapping pathway with wider (2 to 4) channels before activation in each residual block. We further introduce WDSR-B with linear low-rank convolution stack and even widen activation (6 to 9) without computational overhead" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
		 - __identiy path way meaning the activations that is added to the output. Also consider this as the activations in& out of the resiual block. ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__
id:: be24b4bb-f74e-465d-946e-8cfdeffc978d

	 - "simply expanding features before ReLU activation leads to significant improvements for single image super-resolution, beating SR networks with complicated skip connections and concatenations including SRDenseNet [36] and MemNet [33]" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))

	 - "The intuition of our work is that expanding features before ReLU allows more information pass through while still keeps highly non-linearity of deep neural networks. Thus low-level SR features from shallow layers may be easier to propagate to the final layer for better dense pixel value predictions." ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
		 - __增加Relu之前的feature的channel数，来减少Relu对于浅层feature的传播的影响。 ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__
id:: 8c09e72b-b9f2-4367-babb-8fba941badd4

	 - "identity mapping pathway" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
		 - __What is the identiy mapping pathway mentioned in WDSR? ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__
id:: 37a4abe2-3490-42eb-9621-7f7790fc1cca

	 - "WDSR-A, which has a slim identity mapping pathway with wider (2 to 4) channels before activation in each residual block." ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))

	 - "To this end, we propose linear low-rank convolution that factorizes a large convolution kernel into two low-rank convolution kernels. With wider activation and linear low-rank convolutions, we construct our SR network WDSR-B. It has even wider activation (6 to 9) without additional parameters or computation" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))

	 - "We provide three intuitions and related experiments showing that batch normalization, due to 1) mini-batch dependency, 2) different formulations in training and inference and 3) strong regularization side-effects, is not suitable for training SR networks" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
id:: 6b19288d-aa94-4e23-9ddd-b8ad77924fb1
		 - __More evidence why we shouldn't use bn in sisr ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__

	 - "The weight normalization enables us to train SR network with an order of magnitude higher learning rate, leading to both faster convergence and better performance." ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
id:: 4336bcdc-ce88-484a-88b2-7d83fc7365cd
		 - __This explains the higher learning rate in WDSR compared with EDSR. ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__

	 - "In summary, our contributions are as follows. 1) We demonstrate that in residual networks for SISR, wider activation has better performance with same parameter complexity. Without additional computational overhead, we propose network WDSR-A which has wider (2 to 4) activation for better performance. 2) To further improve efficiency, we also propose linear low-rank convolution as basic building block for construction of our SR network WDSR-B. It enables even wider activation (6 to 9) without additional parameters or computation, and boosts accuracy further. 3) We suggest batch normalization [12] is not suitable for training deep SR networks, and introduce weight" ([Yu et al 2018:2](zotero://open-pdf/library/items/XBS8CESG?page=2))
id:: 53d7bfe9-22a8-4e8c-830e-0629c00c10d8
		 - __Summary ([note on p.2](zotero://open-pdf/library/items/XBS8CESG?page=2))__

	 - "normalization [25] for faster convergence and better accuracy. 4) We train proposed WDSR-A and WDSR-B built on the principle of wide activation with weight normalization, and achieve better results on large-scale DIV2K image super-resolution benchmark." ([Yu et al 2018:3](zotero://open-pdf/library/items/XBS8CESG?page=3))

	 - "Upsampling layers Super-resolution involves upsampling operation of image resolution. The first super-resolution network SRCNN [4] applied convolution layers on the pre-upscaled LR image. It is inefficient because all convolutional layers have to compute on high-resolution feature space, yielding S2 times computation than on low-resolution space, where S is the upscaling factor. To accelerate processing speed without loss of accuracy, FSRCNN [3] utilized parametric deconvolution layer at the end of SR network [3], making all convolution layers compute on LR feature space." ([Yu et al 2018:3](zotero://open-pdf/library/items/XBS8CESG?page=3))
		 - __Introduction of SRCNN and FSRCNN ([note on p.3](zotero://open-pdf/library/items/XBS8CESG?page=3))__

	 - "Another non-parametric efficient alternative is pixel shuffling [29] (a.k.a., sub-pixel convolution). Pixel shuffling is also believed to introduce less checkerboard artifacts [22] than the deconvolutional layer." ([Yu et al 2018:3](zotero://open-pdf/library/items/XBS8CESG?page=3))
id:: acd9ed01-c439-46df-aaa6-8b4c12ff4e5c
		 - __Less checkboard effect for Pixel shuffling compared with Deconvolution ([note on p.3](zotero://open-pdf/library/items/XBS8CESG?page=3))__

	 - "We introduce expansion factor before activation as r thus w2 = r w1 . In the vanilla residual networks (e.g., used in EDSR and MDSR) we have w2 = w1 and the number of parameters are 2 w2 k2 in each residual block. The computational (Mult-Add operations) complexity is a constant scaling of parameter numbers when we fix the input patch size. To have same complexity w2 = ^1 ^2 = r ^1 2 , the residual identity mapping pathway need to be slimmed as a factor of pr and the activation can be expanded with pr times meanwhile." ([Yu et al 2018:4](zotero://open-pdf/library/items/XBS8CESG?page=4))
id:: 6e96b4d6-8cd9-4ba1-9293-b30dcc853e51
		 - __The computation stays the same while input to activation function is wider ([note on p.4](zotero://open-pdf/library/items/XBS8CESG?page=4))__

	 - "In WDSR-B (Fig. 1) we first expand channel numbers by using 1 1 and then apply non-linearity (ReLUs) after the convolution layer. We further propose an efficient linear low-rank convolution which factorizes a large convolution kernel to two low-rank convolution kernels. It is a stack of one 1 1 convolution to reduce number of channels and one 3 3 convolution to perform spatial-wise feature extraction." ([Yu et al 2018:5](zotero://open-pdf/library/items/XBS8CESG?page=5))
id:: debf35fc-f9ab-451d-8466-76d98b46afd6
		 - __跟后来我们在nas中使用的模块是一样的 ([note on p.5](zotero://open-pdf/library/items/XBS8CESG?page=5))__

	 - "BN, it will cause following problems. 1) For image super-resolution, commonly only small image patches (e.g. 48 48) and small mini-batch size (e.g. 16) are used to speedup training [7, 14, 17, 19, 33, 36, 42], thus the mean and variance of small image patches differ a lot among mini-batches, making theses statistics unstable, which is demonstrated in the section of experiments. 2) BN is also believed to act as a regularizer and in some cases can eliminate the need for Dropout [12]. However, it is rarely observed that SR networks overfit on training datasets. Instead, many kinds of regularizers, for examples, weight decaying and dropout, are not adopted in SR networks [7, 14, 17, 19, 33, 36, 42]. 3) Unlike image classification tasks where softmax (scale-invariant) is used at the end of networks to make prediction, for image SR, the different formulations of training and testing may deteriorate the accuracy for dense pixel value predictions." ([Yu et al 2018:5](zotero://open-pdf/library/items/XBS8CESG?page=5))
id:: 2fbd1fca-03c2-4a90-9da6-02d3a66f2f08

	 - "Weight normalization," ([Yu et al 2018:5](zotero://open-pdf/library/items/XBS8CESG?page=5))
		 - __没有对feature的约束，需要看abalation study ([note on p.5](zotero://open-pdf/library/items/XBS8CESG?page=5))__

	 - "we slightly modify the network structure and use single convolution layer with kernel size 5 5 that directly take 3 H W LR RGB image/patch as input and output 3S2 H W HR counterparts, where S is the scale. This results in less parameters and computation." ([Yu et al 2018:6](zotero://open-pdf/library/items/XBS8CESG?page=6))

	 - "Different from previous state-of-the-arts [19, 42] where one or more convolutional layers are inserted after upsampling, our proposed WDSR extracts all features in low-resolution stage (Fig. 2)." ([Yu et al 2018:6](zotero://open-pdf/library/items/XBS8CESG?page=6))

- Summary
	 - Network Architecture
		 - [ONNX](https://drive.google.com/file/d/182ntfeD30McRZYBoPs4h87O1ba7surw-/view?usp=sharing)

		 - Wider features before Relu layers.
			 - WDSR-A
				 - Reduce the feature width of identy mapping to keep the computation

			 - WDSR-B
				 - Use 1x1 to expand the channels before Relu.

				 - Use 1x1 Conv + 3x3 Conv to replace the original 3x3 Conv after Relu in Resdual blocks.

			 - ![](../assets/WJlBZrg-ir.png)
id:: 7b8f9e1e-c7fe-40ed-be3b-260f1e040da0

		 - Extracts all features in Low resolution
			 - No convolution after Pixel Shuffle

			 - Direct apply convolution to the HxWx3 input. Less parameters and computation
				 - ![](../assets/SyTkNKmsjn.png)
id:: b3255d35-5ca8-43fc-8114-71bfaeba3afd

		 - Weight Normalization

	 - Results
		 - ![](../assets/9J3fMTA2OV.png)

		 - https://github.com/ychfan/wdsr
			 - ![](../assets/6l6oZg-Shj.png)
