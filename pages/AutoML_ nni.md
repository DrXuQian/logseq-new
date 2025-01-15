---
title: AutoML: nni
---
- Tricky points:
	 - 1. No gradient for the arch parameters of the first convolution building block
	 - 2. Compute loss in metrics again would cause gpu mem to explode.
		 - print("1:{}".format(torch.cuda.memory_allocated(0)))