---
title: MKLDNN memory layout
---

- #MKLDNN

- This is because we need to make fully use of the memory already cached. And the cache in and cache out takes lots of time as proposed in [[Im2col]] and [[cache and register]]

- NCHW (caffe, Tensorflow), NHWC (TensorFlow default) and CHWN (neon) layouts
	 - ![mem_fmt_img2.png](https://oneapi-src.github.io/oneDNN/v1.0/mem_fmt_img2.png)
id:: 7c603991-8487-4ad5-be81-0929418cf9d0

- blocked layout: Plain layouts give great flexibility and are very convenient for use. That's why most of the frameworks and applications use either the **NCHW** or **NHWC** layout. However, depending on the operation that is performed on data, it might turn out that those layouts are sub-optimal from the performance perspective.
	 - The most popular Intel MKL-DNN data format is **nChw16c** on AVX512+ systems and **nChw8c** on SSE4.1+ systems. As one might guess from the name the only dimension that is blocked is channels and the block size is either 16 in the former case or 8 in the later case.

	 - It is obvious that for AVX512, the registers are of 512 bytes, so we can store 16 full-precision floating point variables in that register. 

	 - ![mem_fmt_blk.png](https://oneapi-src.github.io/oneDNN/v1.0/mem_fmt_blk.png)

	 - ![mem_fmt_padded_blk.png](https://oneapi-src.github.io/oneDNN/v1.0/mem_fmt_padded_blk.png)

- memory propagation
	 - The memory of input weight and output of the convolution pd need to be reordered before actual convolution for best performance.

- [[AVX512-VNNI]] instruction use the final layout and take advantage of that nChw16c layout, because 16 single precision floating point would be 512 bits in length, and would fit in the register fully.
