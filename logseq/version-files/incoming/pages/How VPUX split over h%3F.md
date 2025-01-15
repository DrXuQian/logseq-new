title:: How VPUX split over h?

-
- There is a constraint of HxW need to be 4x. But for the last tile, that constraint doesn't need to be met.
	- For example:
		- 14x14 split in VPUX compiler:
			- VPUX compiler would split that into 6x8 and 8x8 since there is constraint on HxW need to be multiplier of 4
		- 7x7 split in VPUX compiler:
			- This can be split to 4x4 and 4x3 and 3x4 and 3x3. The last tile is 3x3, and it also works despite the HxW is not 4x.
- For Conv 28x28->14x14 followed by Conv 14x14->7x7
	- The first Conv input 28x28 would split to 14x28 and 14x28. The second Conv input 14x14 would split to 6x14 and 8x14.
	- So there is a mismatch of the feature map for the first Conv output [7x14, 7x14] and the second Conv input [6x14, 8x14].
	- In VPUX, this can be handled by reading the corresponding input for the desired output from the other tile, so no broadcast is needed in this case. Kind of like halo region.