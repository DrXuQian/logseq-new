- [[Tiled op in Nbperf]]
	- The thing is, in current code base. If we split the convolution in two. The padding would still apply to the convolution, which doesn't make sense.
	- To fully describe the convolution in DPU:
		- split the convolution featuremap evenly, the padding is added as parameters in the boundary split
		- DONE What exactly does fathom or VPUX provide for this?
		  :LOGBOOK:
		  CLOCK: [2023-02-12 Sun 20:04:00]--[2023-02-12 Sun 20:06:53] =>  00:02:53
		  CLOCK: [2023-02-12 Sun 20:07:19]--[2023-02-14 Tue 09:51:47] =>  37:44:28
		  :END:
		- whatever registers that padding needs
		- Input feature without padding
		- output feature size
		- weight
	- For tiled ops:
		- DONE Check out how VPUX compiler split the kernels with Yingyue
		  :LOGBOOK:
		  CLOCK: [2023-02-12 Sun 20:10:20]
		  CLOCK: [2023-02-12 Sun 20:10:22]--[2023-02-14 Tue 09:51:38] =>  37:41:16
		  :END:
		- The padding is different from original operator
		- The tiled ops need to be like:
			- RN50 Filter: 64x3x7x7 Strides: {2, 2} Padding: {bottom = 3, left = 2, right = 3, top = 2}
			- Tile 0 input: 1x3x59x224, padding {bottom = 0, left = 2, right = 3, top = 2}
			- Tile 1 input: 1x3x61x224, padding {bottom = 0, left = 2, right = 3, top = 0}
			- Tile 2 input: 1x3x61x224, padding {bottom = 0, left = 2, right = 3, top = 0}
			- Tile 3 input: 1x3x58x224, padding {bottom = 3, left = 2, right = 3, top = 0}
- How does the padding happen in VPU hardware?
	- [1/19 3:58 PM] Qian, Xu
		- Hanrahan, Niall, Grymel, Martin, Is there any document about how the padding of the convolution is done on DPU?
	- [1/19 4:36 PM] Hanrahan, Niall
		- Hi Xu - padding s controlled via the following registers:
		- ![image.png](../assets/image_1676203277000_0.png)
		- max supported pad is up to half the size of the kernel, so for 4x4 kernel max pad is 2 and for 3x3 kernel max pad is 1
	- [1/19 4:37 PM] Qian, Xu
		- Would the padding take extra memory on CMX?
	- [1/19 4:38 PM] Hanrahan, Niall]
		- no - padding does not need CMX memory alloaction
	- [1/19 4:38 PM] Qian, Xu
		- Got you. Thanks!
- For Vertical fusion, if we construct according to [[Tiled op in Nbperf]], the graph would mismatch, so
	- First generate the vf subgraph
	- Then get the reversed dfs order
	- Loop the graph with the reversed dfs order and derive the input from the output shape size and re-generate the tiled ops.
	- Construct the whole graph.
- After this, we would need to consider how to get the cost of vf subgraph. Since there is going to be duplicate computation for halo region.
  id:: 63ea2d2b-5eb0-409f-a329-122fda3ad064
	- Cost for subgraph:
		- complexity analysis:
			- For every node in the vf subgraph, we can set that as vf-terminal for every node. That is the n-of-node^2.
		- time cost:
			- Compare vf subgraph time and vf subgraph split in to n-parts time(in the middle point of the subgraph)
				- extra compute time = vf subgraph compute time - original compute time
				- saved dma time = middle point node output feature map * 2 / dma freq
			-