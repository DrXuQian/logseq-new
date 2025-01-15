- #tiling
- Tiling strategy in KMB
	- Suppose Activation size + Output Activation size + Kernel size > CMX memory size
		- Take one of the following options:
			- option1: Splitting Activation along the height
			- option2: Splitting Weight along the output channel
		- Target:
			- pipeline流水流起来，也就是在进行当前运算时，预取一部分后续的数据，然后等当前数据运算完成之后释放掉，再读取后续的数据。
- Splitting strategy in KMB
	- Natural for NCHW to split over K and NHWC to split over H
	- Other split can be achieved through [[Strided DMA]]
	- [[SOH]]
		- Split over the dimension of Height, weight need to duplicate across clusters
	- [[SOK]]
		- Split over the dimension of Kernel, activation need to duplicate across clusters
		  :LOGBOOK:
		  CLOCK: [2022-02-24 Thu 20:59:34]--[2022-02-28 Mon 11:29:06] =>  86:29:32
		  CLOCK: [2022-02-28 Mon 11:29:07]--[2022-02-28 Mon 11:29:07] =>  00:00:00
		  CLOCK: [2022-02-28 Mon 11:29:08]
		  :END: