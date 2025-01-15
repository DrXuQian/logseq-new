---
title: DLSS
---

- [GTC 2020: DLSS 2.0 - Image Reconstruction for Real-time Rendering with Deep Learning | NVIDIA Developer](https://developer.nvidia.com/gtc/2020/video/s22698)

- Training data:
	 - input: low resolution image rendered

	 - label: high resolution image rendered
		 - ![](../assets/Bs13VNuY1M.png)

- performance:
	 - 1.5ms @4k on 2080ti
		 - render at a low resolution + DLSS for high resolution image 

	 - comparable image quality with native rendered high resolution image (for example, 540p -> 1080p using DLSS comparable to 1080p using TAA)
