date:: [[Jul 10th, 2017]]
title:: @Enhanced Deep Residual Networks for Single Image Super-Resolution
item-type:: [[journalArticle]]
access-date:: 2022-01-26T13:00:31Z
original-title:: Enhanced Deep Residual Networks for Single Image Super-Resolution
language:: en
url:: https://arxiv.org/abs/1707.02921v1
authors:: [[Bee Lim]], [[Sanghyun Son]], [[Heewon Kim]], [[Seungjun Nah]], [[Kyoung Mu Lee]]
library-catalog:: arxiv.org
links:: [Local library](zotero://select/library/items/5LPV9F2T), [Web library](https://www.zotero.org/users/9063164/items/5LPV9F2T)

- [[Abstract]]
	- Recent research on super-resolution has progressed with the development of deep convolutional neural networks (DCNN). In particular, residual learning techniques exhibit improved performance. In this paper, we develop an enhanced deep super-resolution network (EDSR) with performance exceeding those of current state-of-the-art SR methods. The significant performance improvement of our model is due to optimization by removing unnecessary modules in conventional residual networks. The performance is further improved by expanding the model size while we stabilize the training procedure. We also propose a new multi-scale deep super-resolution system (MDSR) and training method, which can reconstruct high-resolution images of different upscaling factors in a single model. The proposed methods show superior performance over the state-of-the-art methods on benchmark datasets and prove its excellence by winning the NTIRE2017 Super-Resolution Challenge.
- [[Attachments]]
	- [Full Text PDF](https://arxiv.org/pdf/1707.02921) {{zotero-imported-file LC2N7Q28, "Lim 等。 - 2017 - Enhanced Deep Residual Networks for Single Image S.pdf"}}
- [[Notes]]
	- **Extracted Annotations (2022/1/28 下午3:27:34)**
	  
	  "In this paper, we develop an enhanced deep super-resolution network (EDSR) with performance exceeding those of current state-of-the-art SR methods. The significant performance improvement of our model is due to optimization by removing unnecessary modules in conventional residual networks. The performance is further improved by expanding the model size while we stabilize the training procedure." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	  
	  "Generally, the relationship between I LR and the original high-resolution image I HR can vary depending on the situation. Many studies assume that I LR is a bicubic downsampled version of I HR, but other degrading factors such as blur, decimation, or noise can also be considered for practical applications." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	  
	  "VDSR [11] can handle super-resolution of several scales jointly in the single network. Training the VDSR model with multiple scales boosts the performance substantially and outperforms scale-specific training, implying the redundancy among scale-specific models." ([Lim et al 2017:2](zotero://open-pdf/library/items/LC2N7Q28?page=2))
	  
	  "In Fig. 2, we compare the building blocks of each network model from original ResNet [9], SRResNet [14], and our proposed networks. We remove the batch normalization layers from our network as Nah et al.[19] presented in their image deblurring work. Since batch normalization layers normalize the features, they get rid of range flexibility from networks by normalizing the features, it is better to remove them. We experimentally show that this simple modification increases the performance substantially as detailed in Sec. 4." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *Which means that using proxylessnas is not sufficient for SISS tasks since the output need to unify for proxylessnas. Perhaps we can try other nas methods for SISS. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "General CNN architecture with depth (the number of layers) B and width (the number of feature channels) F occupies roughly O(BF ) memory with O(BF 2 ) parameters. Therefore, increasing F instead of B can maximize the model capacity when considering limited computational resources." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *several layers of parameters: k*k*f*f*b
	  memory for activation b*f*h*w
	  ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "We resolve this issue by adopting the residual scaling [24] with factor 0.1. In each residual block, constant scaling layers are placed after the last convolution layers. These modules stabilize the training procedure greatly when using a large number of filters." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *How is this helping stablize the training process? Hard to believe actually. In our experiment, we did not use this method and set scaling to 1. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "In our final single-scale model (EDSR), we expand the baseline model by setting B = 32, F = 256 with a scaling factor 0.1." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  "When training our model for upsampling factor3 and 4, we initialize the model parameters with pre-trained2 network. This pre-training strategy accelerates the training and improves the final performance" ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  "From the observation in Fig. 4, we conclude that superresolution at multiple scales is inter-related tasks. We further explore this idea by building a multi-scale architecture that takes the advantage of inter-scale correlation as VDSR [11] does." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "First, pre-processing modules are located at the head of networks to reduce the variance from input images of different scales." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  *so, the target image is only one image with a fixed resolution, we have different downsampled images (2x downsampled, 3x downsampled, 4x downsampled) as network input. Thus, we need three pre-processing modules handling these three resolution inputs. ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))*
	  
	   
	  
	  "Each of pre-processing module consists of two residual blocks with 5 5 kernels. By adopting larger kernels for pre-processing modules, we can keep the scale-specific part shallow while the larger receptive field is covered in early stages of networks." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  *Not fully understood. Use larger kernels because we want to keep the scale dependent preprocessing part shallow.
	  According to MDSR structure https://github.com/sanghyun-son/EDSR-PyTorch/blob/master/src/model/mdsr.py.
	  The preprocessing for all resolution are the same, 5x5 resblocks ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))*
	  
	   
	  
	  "At the end of the multi-scale model, scale-specific upsampling modules are located in parallel to handle multi-scale reconstruction." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "For training, we use the RGB input patches of size 48 48 from LR image with the corresponding HR patches. We augment the training data with random horizontal flips and 90 rotations. We pre-process all the images by subtracting the mean RGB value of the DIV2K dataset." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "with ADAM optimizer [13] by setting 1 = 0:9, 2 = 0:999, and = 108 . We set minibatch size as 16. The learning rate is initialized as 104 and halved at every 2 105 minibatch updates" ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	  
	  "At each update of training a multi-scale model (MDSR), we construct the minibatch with a randomly selected scale among2;3 and4. Only the modules that correspond to the selected scale are enabled and updated." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	  
	  "L1 loss instead of L2. Minimizing L2 is generally preferred since it maximizes the PSNR." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
- date:: [[Jul 10th, 2017]]
  title:: @Enhanced Deep Residual Networks for Single Image Super-Resolution
  item-type:: [[journalArticle]]
  access-date:: 2022-01-26T13:00:31Z
  original-title:: Enhanced Deep Residual Networks for Single Image Super-Resolution
  language:: en
  url:: https://arxiv.org/abs/1707.02921v1
  authors:: [[Bee Lim]], [[Sanghyun Son]], [[Heewon Kim]], [[Seungjun Nah]], [[Kyoung Mu Lee]]
  library-catalog:: arxiv.org
  links:: [Local library](zotero://select/library/items/5LPV9F2T), [Web library](https://www.zotero.org/users/9063164/items/5LPV9F2T)
- [[Abstract]]
	- Recent research on super-resolution has progressed with the development of deep convolutional neural networks (DCNN). In particular, residual learning techniques exhibit improved performance. In this paper, we develop an enhanced deep super-resolution network (EDSR) with performance exceeding those of current state-of-the-art SR methods. The significant performance improvement of our model is due to optimization by removing unnecessary modules in conventional residual networks. The performance is further improved by expanding the model size while we stabilize the training procedure. We also propose a new multi-scale deep super-resolution system (MDSR) and training method, which can reconstruct high-resolution images of different upscaling factors in a single model. The proposed methods show superior performance over the state-of-the-art methods on benchmark datasets and prove its excellence by winning the NTIRE2017 Super-Resolution Challenge.
- [[Attachments]]
	- [Full Text PDF](https://arxiv.org/pdf/1707.02921) {{zotero-imported-file LC2N7Q28, "Lim 等。 - 2017 - Enhanced Deep Residual Networks for Single Image S.pdf"}}
- [[Notes]]
	- **Extracted Annotations (2022/1/28 下午3:27:34)**
	  
	  "In this paper, we develop an enhanced deep super-resolution network (EDSR) with performance exceeding those of current state-of-the-art SR methods. The significant performance improvement of our model is due to optimization by removing unnecessary modules in conventional residual networks. The performance is further improved by expanding the model size while we stabilize the training procedure." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	  
	  "Generally, the relationship between I LR and the original high-resolution image I HR can vary depending on the situation. Many studies assume that I LR is a bicubic downsampled version of I HR, but other degrading factors such as blur, decimation, or noise can also be considered for practical applications." ([Lim et al 2017:1](zotero://open-pdf/library/items/LC2N7Q28?page=1))
	  
	  "VDSR [11] can handle super-resolution of several scales jointly in the single network. Training the VDSR model with multiple scales boosts the performance substantially and outperforms scale-specific training, implying the redundancy among scale-specific models." ([Lim et al 2017:2](zotero://open-pdf/library/items/LC2N7Q28?page=2))
	  
	  "In Fig. 2, we compare the building blocks of each network model from original ResNet [9], SRResNet [14], and our proposed networks. We remove the batch normalization layers from our network as Nah et al.[19] presented in their image deblurring work. Since batch normalization layers normalize the features, they get rid of range flexibility from networks by normalizing the features, it is better to remove them. We experimentally show that this simple modification increases the performance substantially as detailed in Sec. 4." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *Which means that using proxylessnas is not sufficient for SISS tasks since the output need to unify for proxylessnas. Perhaps we can try other nas methods for SISS. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "General CNN architecture with depth (the number of layers) B and width (the number of feature channels) F occupies roughly O(BF ) memory with O(BF 2 ) parameters. Therefore, increasing F instead of B can maximize the model capacity when considering limited computational resources." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *several layers of parameters: k*k*f*f*b
	  memory for activation b*f*h*w
	  ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "We resolve this issue by adopting the residual scaling [24] with factor 0.1. In each residual block, constant scaling layers are placed after the last convolution layers. These modules stabilize the training procedure greatly when using a large number of filters." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  *How is this helping stablize the training process? Hard to believe actually. In our experiment, we did not use this method and set scaling to 1. ([note on p.3](zotero://open-pdf/library/items/LC2N7Q28?page=3))*
	  
	   
	  
	  "In our final single-scale model (EDSR), we expand the baseline model by setting B = 32, F = 256 with a scaling factor 0.1." ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  "When training our model for upsampling factor3 and 4, we initialize the model parameters with pre-trained2 network. This pre-training strategy accelerates the training and improves the final performance" ([Lim et al 2017:3](zotero://open-pdf/library/items/LC2N7Q28?page=3))
	  
	  "From the observation in Fig. 4, we conclude that superresolution at multiple scales is inter-related tasks. We further explore this idea by building a multi-scale architecture that takes the advantage of inter-scale correlation as VDSR [11] does." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "First, pre-processing modules are located at the head of networks to reduce the variance from input images of different scales." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  *so, the target image is only one image with a fixed resolution, we have different downsampled images (2x downsampled, 3x downsampled, 4x downsampled) as network input. Thus, we need three pre-processing modules handling these three resolution inputs. ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))*
	  
	   
	  
	  "Each of pre-processing module consists of two residual blocks with 5 5 kernels. By adopting larger kernels for pre-processing modules, we can keep the scale-specific part shallow while the larger receptive field is covered in early stages of networks." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  *Not fully understood. Use larger kernels because we want to keep the scale dependent preprocessing part shallow.
	  According to MDSR structure https://github.com/sanghyun-son/EDSR-PyTorch/blob/master/src/model/mdsr.py.
	  The preprocessing for all resolution are the same, 5x5 resblocks ([note on p.4](zotero://open-pdf/library/items/LC2N7Q28?page=4))*
	  
	   
	  
	  "At the end of the multi-scale model, scale-specific upsampling modules are located in parallel to handle multi-scale reconstruction." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "For training, we use the RGB input patches of size 48 48 from LR image with the corresponding HR patches. We augment the training data with random horizontal flips and 90 rotations. We pre-process all the images by subtracting the mean RGB value of the DIV2K dataset." ([Lim et al 2017:4](zotero://open-pdf/library/items/LC2N7Q28?page=4))
	  
	  "with ADAM optimizer [13] by setting 1 = 0:9, 2 = 0:999, and = 108 . We set minibatch size as 16. The learning rate is initialized as 104 and halved at every 2 105 minibatch updates" ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	  
	  "At each update of training a multi-scale model (MDSR), we construct the minibatch with a randomly selected scale among2;3 and4. Only the modules that correspond to the selected scale are enabled and updated." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))
	  
	  "L1 loss instead of L2. Minimizing L2 is generally preferred since it maximizes the PSNR." ([Lim et al 2017:5](zotero://open-pdf/library/items/LC2N7Q28?page=5))