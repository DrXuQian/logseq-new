---
title: AVX512-VNNI
---

- https://en.wikichip.org/wiki/x86/avx512_vnni

- Registers in AVX512
	 - The width of the [SIMD](https://en.wikipedia.org/wiki/SIMD) register file is increased from 256 bits to 512 bits, and expanded from 16 to a total of 32 registers ZMM0-ZMM31. These registers can be addressed as 256 bit YMM registers from AVX extensions and 128-bit XMM registers from [Streaming SIMD Extensions](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions), and legacy AVX and SSE instructions can be extended to operate on the 16 additional registers XMM16-XMM31 and YMM16-YMM31 when using EVEX encoded form.

	 - This is real interesting that there is no extra YMM and XMM registers. AVX512 only provided extension instruction to use ZMMs as YMM and XMM.

	 - ![](../assets/Pwba0Eg8sh.png)

- VNNI
	 - ![vnni-vpdpwssd.svg](https://en.wikichip.org/w/images/thumb/f/fa/vnni-vpdpwssd.svg/600px-vnni-vpdpwssd.svg.png)

	 - ![vnni-vpdpbusd.svg](https://en.wikichip.org/w/images/thumb/a/a6/vnni-vpdpbusd.svg/600px-vnni-vpdpbusd.svg.png)

	 - ![vnni-vpdpbusd-i.svg](https://en.wikichip.org/w/images/thumb/c/cd/vnni-vpdpbusd-i.svg/400px-vnni-vpdpbusd-i.svg.png)
