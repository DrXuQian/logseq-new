---
title: p: Understanding the Performance of Small Convolution Operations for CNN on Intel Architecture
---
- ![](../assets/oHwyXY2q9K.pdf)
id:: 0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6
	 - t all specialized version sin L1 instruction cache {{1  5ZFn3s-nW}} [📑](((0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6)))
	 - e input parameters to convolution includingN,C,K,H,W,R,S,u, andvvary signicantly across benchmarks, making it almost impossible to achieve peak performance via static compilation. {{1  5j0S6KWzf}} [📑](((0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6)))
	 - Forexample, the loops to tile and their tiling factors can not be deter-mined statically without knowing the actual values of the parame-ters. {{1  k7jBf3fGl}} [📑](((0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6)))
	 - At runtime, we apply special data for-mats for input/output/weight data, compiler optimizations such as tiling and register blocking to optimize the seven loop nests,runtime code specialization, and soware pref etching {{1  b0ZEt1VLG}} [📑](((0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6)))
	 - First, the address calculations of the corresponding tensor blocks involve integer multiplication sand additions. Second, calculating the addresses of the tensor blocks to be pref etched entails complicated conditional statements. We alleviate these two issues by developing a technique we callkernelstreams. During the JIT-ing phase we perform a dry run of the con-volution loops and we compute streams of address offsets for the kernel call arguments.  {{2  Q-YYtH6XS}} [📑](((0d5bd56b-4fb8-4651-9d33-f7a8dd2b9de6)))
-
	 - Highlight
- Notes
	 -