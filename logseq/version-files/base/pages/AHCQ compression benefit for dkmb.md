- Contexts:
	- [10:26 AM] Crews, Darren S
	- can you send me the final version of this paper, I only have a pre-submitted version: Auto Hessian Aware Channel-wise Quantization of Neural
	  Networks
	- [10:27 AM] Qian, Xu
	- You can actually get it online: [https://www.emc2-ai.org/assets/docs/virtual-20/emc2-virtual20-paper-5.pdf](https://www.emc2-ai.org/assets/docs/virtual-20/emc2-virtual20-paper-5.pdf)
	- like 1
	- [10:27 AM] Crews, Darren S
	- I was thinking that we could re-do this on KMB showing the benefit of compressed weights and the huffman decompressor block and publish it as a white paper
	- [10:29 AM] Li, Victor Y
	- So, we want to show the benefit from compressed weight?
	- [10:29 AM] Crews, Darren S
	- yeah
	- [10:30 AM] Qian, Xu
	- The weights are channel-wise mixed precision. So we are going to use int8 as dtype and see the compression benefit for this method?
	- [10:30 AM] Crews, Darren S
	- yeah, that's what I was thinking
	- [11:14 AM] Qian, Xu
	- In that case, we need to actually run the quantized weights through a huffman encoder block and see the actual benefit.
	- [11:14 AM] Crews, Darren S
	- yeah, if we did that, that would be the best way to do it, we do have the c model
	-