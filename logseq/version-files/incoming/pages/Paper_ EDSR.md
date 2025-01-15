---
title: Paper: EDSR
---

- Metadata
	- [[Class]]: Computer Vision
	- [[Topic]]: SISR
	- [[Lecturer]]: Bee Lim Sanghyun Son Heewon Kim Seungjun Nah Kyoung Mu Lee
	- [[Date]]: 2017 july 10
	- [[Keywords]]: [[SISR]] [[EDSR]] [[Super Resolution]]
- Recall Questions
	- What is the highlight of EDSR paper? #ques1 #card
		- get rid of bn instead of normal residual block;
		- share the parameters of 2x and 3x and 4x
	- Why F is more important than B in this paper?  #ques1 #card
		- activation is O(FB) while parameters is O(BF**2)
		- same activation would have more parameters when F > B
	- What is the structure of WDSR?  #ques1 #card
	  card-last-interval:: -1
	  card-repeats:: 1
	  card-ease-factor:: 2.5
	  card-next-schedule:: 2022-03-04T16:00:00.000Z
	  card-last-reviewed:: 2022-03-04T11:32:15.786Z
	  card-last-score:: 1
		- as shown in block ((2006c9a0-54c3-416b-adad-0da5123bb2f6))
	- What loss function EDSR used? Why?  #ques1 #card
		- DONE L1 instead of L2
- Notes
	- **Extracted Annotations (2022/1/28 下午3:27:34)**
	- "In this paper, we develop an enhanced deep super-resolution network (EDSR) with performance exceeding those of current state-of-the-art SR methods. The significant performance improvement of our model is due to optimization by removing unnecessary modules in conventional residual networks. The performance is further improved by expanding the model size while we stabilize the training procedure." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	- "Generally, the relationship between I LR and the original high-resolution image I HR can vary depending on the situation. Many studies assume that I LR is a bicubic downsampled version of I HR, but other degrading factors such as blur, decimation, or noise can also be considered for practical applications." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	- "VDSR [11] can handle super-resolution of several scales jointly in the single network. Training the VDSR model with multiple scales boosts the performance substantially and outperforms scale-specific training, implying the redundancy among scale-specific models." ([Lim et al 2017:2](zotero://open-pdf/library/items/LC2N7Q28?page=2))
	- "In Fig. 2, we compare the building blocks of each network model from original ResNet [9], SRResNet [14], and our proposed networks. We remove the batch normalization layers from our network as Nah et al.[19] presented in their image deblurring work. Since batch normalization layers normalize the features, they get rid of range flexibility from networks by normalizing the features, it is better to remove them. We experimentally show that this simple modification increases the performance substantially as detailed in Sec. 4." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
		- __Which means that using proxylessnas is not sufficient for SISR tasks since the output need to unify for proxylessnas. Perhaps we can try other nas methods for SISR. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))__
	- "General CNN architecture with depth (the number of layers) B and width (the number of feature channels) F occupies roughly O(BF ) memory with O(BF 2 ) parameters. Therefore, increasing F instead of B can maximize the model capacity when considering limited computational resources." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
		- __several layers of parameters: k*k*f*f*b memory for activation b*f*h*w([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))__
	- "We resolve this issue by adopting the residual scaling [24] with factor 0.1. In each residual block, constant scaling layers are placed after the last convolution layers. These modules stabilize the training procedure greatly when using a large number of filters." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
		- __How is this helping stablize the training process? Hard to believe actually. In our experiment, we did not use this method and set scaling to 1. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))__
	- "In our final single-scale model (EDSR), we expand the baseline model by setting B = 32, F = 256 with a scaling factor 0.1." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	- "When training our model for upsampling factor3 and 4, we initialize the model parameters with pre-trained2 network. This pre-training strategy accelerates the training and improves the final performance" ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	- "From the observation in Fig. 4, we conclude that superresolution at multiple scales is inter-related tasks. We further explore this idea by building a multi-scale architecture that takes the advantage of inter-scale correlation as VDSR [11] does." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	- "First, pre-processing modules are located at the head of networks to reduce the variance from input images of different scales." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
		- __so, the target image is only one image with a fixed resolution, we have different downsampled images (2x downsampled, 3x downsampled, 4x downsampled) as network input. Thus, we need three pre-processing modules handling these three resolution inputs. ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))__
	- "Each of pre-processing module consists of two residual blocks with 5 5 kernels. By adopting larger kernels for pre-processing modules, we can keep the scale-specific part shallow while the larger receptive field is covered in early stages of networks." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
		- __Not fully understood. Use larger kernels because we want to keep the scale dependent preprocessing part shallow.According to MDSR structure https://github.com/sanghyun-son/EDSR-PyTorch/blob/master/src/model/mdsr.py.The preprocessing for all resolution are the same, 5x5 resblocks ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))__
	- "At the end of the multi-scale model, scale-specific upsampling modules are located in parallel to handle multi-scale reconstruction." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	- "For training, we use the RGB input patches of size 48 48 from LR image with the corresponding HR patches. We augment the training data with random horizontal flips and 90 rotations. We pre-process all the images by subtracting the mean RGB value of the DIV2K dataset." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	- "with ADAM optimizer [13] by setting 1 = 0:9, 2 = 0:999, and = 108 . We set minibatch size as 16. The learning rate is initialized as 104 and halved at every 2 105 minibatch updates" ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	- "At each update of training a multi-scale model (MDSR), we construct the minibatch with a randomly selected scale among2;3 and4. Only the modules that correspond to the selected scale are enabled and updated." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	- "L1 loss instead of L2. Minimizing L2 is generally preferred since it maximizes the PSNR." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
- Summary
	- Get rid of batch normalization layers in Residual network structures as the following figure shows.
		- ![](../assets/oCLXcFBTza.png)
		- Benefit:
			- BN consumes the same memory as convolution layers for activations. Without BN, we can use larger network structures.
			- BN normalized the features, in other words, BN get rid of the range flexibility of a given layer. This can harm the performance for SS tasks.
				- This means we are voting exactly against this proposed method in ProxylessNAS becasue we need to normalize the features of different building blocks using BN, or else, proxylessnas training wouldn't converge.
	- Network architecture
	  id:: 2006c9a0-54c3-416b-adad-0da5123bb2f6
		- ![](../assets/i67PgOiraX.png)
		- Key takeaways
			- B = 32
			- F = 256
			- scaling factor 0.1
			- [ONNX](https://drive.google.com/file/d/19hTeK36r6rPCb9Gk-8i3IjPC47gsB0cQ/view?usp=sharing)
			- [EDSR_small.onnx](https://drive.google.com/file/d/18U6iDU-Buve6dYG3McUFGG0suTVCvtvn/view?usp=sharing)
	- The network parameter of x3 and x4 EDSR can share the parameters of x2 EDSR. Muti-Scale architecture MDSR is a revision of EDSR where the body part is shared between different scales. The final upsampling part is different, the pre-processing part is also different.
		- EDSR use 5x5 kernels in preprocessing elements to keep the scale-dependent part of network shallow. [Code](https://github.com/sanghyun-son/EDSR-PyTorch/blob/master/src/model/mdsr.py.)
	- Training details
		- Data generation
			- First, low resolution is downsampled from super resolution by bicubic.
			- Second, in EDSR, we take 48x48 patches from the original image and do some augmentations including rotaion, flipping. [Code](https://github.com/sanghyun-son/EDSR-PyTorch/blob/9d3bb0ec620ea2ac1b5e5e7a32b0133fbba66fd2/src/data/srdata.py#L135)
			- Then substracting 112.
		- Loss function
			- L1 loss instead of L2
		- Learning Rate scheduling
			- ADAM optimizer by setting beta1 = 0.9, beta2 = 0.999, and episilon = 108 . We set minibatch size as 16. The learning rate is initialized as 1e-4 and halved at every 2e5 minibatch updates.
	- Results
		- ![](../assets/IYgwg7-LQf.png)
			- EDSR_baseline is for B=16, F=64
			- EDSRx2 is for B=32, F=256