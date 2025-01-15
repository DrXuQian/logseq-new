title:: What is the padding when query cost model for cycles?

- ![image.png](../assets/image_1675338956577_0.png)
- For the given convolution:
	- RN50 Filter: 64x3x7x7
	- Padding: {bottom = 3, left = 2, right = 3, top = 2}
	- for vpu2p7, when one tile need the halo region, the tile directly query the other tiles for the input that is needed to generate the output.
		- 4 tiles split:
			- oh: 95 to 24, 24, 24, 23.
			- ih: 190 to 48, 48, 48, 46.
		- DONE How do we get the halo region related cost from VPUNN?
		  :LOGBOOK:
		  CLOCK: [2023-02-02 Thu 20:01:15]--[2023-02-02 Thu 20:04:09] =>  00:02:54
		  CLOCK: [2023-02-13 Mon 21:38:47]--[2023-02-14 Tue 09:53:01] =>  12:14:14
		  :END:
		- Back infer from the bottom of the vf network (from Yingyue)
	- for vpu4p0 with halo region in each tile already there (this is automatically done by the hw), splits should be
		- Tile 0 input: 1x3x59x224
		- Tile 1 input: 1x3x61x224
		- Tile 2 input: 1x3x61x224
		- Tile 3 input: 1x3x58x224
	- And the padding would be:
		- Tile 0 input: 1x3x59x224, padding {bottom = 0, left = 2, right = 3, top = 2}
		- Tile 1 input: 1x3x61x224, padding {bottom = 0, left = 2, right = 3, top = 0}
		- Tile 2 input: 1x3x61x224, padding {bottom = 0, left = 2, right = 3, top = 0}
		- Tile 3 input: 1x3x58x224, padding {bottom = 3, left = 2, right = 3, top = 0}