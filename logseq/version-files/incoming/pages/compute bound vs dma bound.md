## NPU world
	- ### Prefetching Weight
		- ideally you would check the previous layer compute time with the weight fetch of the next layer
			- comparing compute time of layer i with dma of layer i +1
			- 如果后一个卷积的权重放不下，那就tile下一个卷积，让tile0的权重能够放下。
			- 如果连续两个tile conv，那么应该怎么做？prefetching 下一个卷积的tile0需要的 activation + weights
- ## GPU World
	- How to determine a problem is compute or memory bound? #card
		-