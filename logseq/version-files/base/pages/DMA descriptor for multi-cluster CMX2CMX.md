- Contexts:
  collapsed:: true
	- [1/13 4:46 PM] Qian, Xu
		- ==yaobo，VPUX compiler构建CMX-->DDR的stride DMA时候，如果source是多个CMX的cluster，构建出来的DMA descripter会是多个吗？==
	- [1/13 4:51 PM] Yao, Shaojun
		- ==是多个DMA descripter，因为你每个cluster都需要一个DMA吧==
	- [1/13 4:51 PM] Qian, Xu
		- ==哦哦，明白，如果DDR-->CMX的时候呢，多个dst的CMX==
	- [1/13 4:55 PM] Yao, Shaojun
		- ==这个一般也是多个，但如果是DDR上的同一份数据要copy到多个CMX上的相同offset的地址上，可以用一个DMA==
	- [1/13 4:56 PM] Qian, Xu
		- 如果CMX2DDR的时候offset一样，可以一个DMA吗？
	- [1/13 4:56 PM] Yao, Shaojun
		- 不行
	- [1/13 4:56 PM] Yao, Shaojun
		- 这个是DMA 的 broadcast
	- [1/13 4:57 PM] Qian, Xu
		- 你们现在有CMX2CMX的DMA吗？这个也是多个descriptor把
	- [1/13 4:58 PM] Yao, Shaojun
		- 这个和 CMX2DDR/DDR2CMX 的分析是一样的
	- [1/13 4:58 PM] Yao, Shaojun
		- 一些情况下有 CMX2CMX的DMA
	- [1/13 4:59 PM] Yao, Shaojun
		- ==我们还不支持 CMX2CMX 两边同时在多个cluster上的情况==
	- [1/13 4:59 PM] Qian, Xu
		- OK，了解
- Multi-cluster DMA desc in VPUX:
	- CMX2DDR, multiple desc
	- DDR2CMX, same offset, one desc; else, multiple desc
	- CMX2CMX, multi tile not supported in VPUX
- DONE What happens if shave output need to broadcast to other tiles
  :LOGBOOK:
  CLOCK: [2023-02-13 Mon 19:28:16]--[2023-02-13 Mon 21:37:35] =>  02:09:19
  :END:
- This will trigger spill, suppose the MVN is split over SOH, then the next operator needs HK Switch.
- Case Study of depth2space:
	- op1-->depth2space-->op2
	- op1-->copy-->op2
	- Should never broadcast output, the task should be generated based on op1 output and op2 input.
	- DONE Is there any constraint on such things?
	  :LOGBOOK:
	  CLOCK: [2023-02-13 Mon 20:30:29]
	  CLOCK: [2023-02-13 Mon 20:30:31]--[2023-02-13 Mon 21:37:36] =>  01:07:05
	  :END:
	- Currently D2S only support SOH only. If previous layer is not SOH, trigger spilling. If previous layer is SOH, split D2S over H.
- Steps of adding copy in nbperf:
	- Suppose we transform depth2space to copy
	- copy --> stride dma
-