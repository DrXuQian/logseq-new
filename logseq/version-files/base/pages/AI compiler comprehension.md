---
title: AI compiler comprehension
---
- Why do we need deep learning compiler? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:11:00.261Z
  card-last-reviewed:: 2023-01-28T15:11:00.262Z
  card-last-score:: 5
	- 不需要每一个硬件都去做specific的优化。
	- 有一些优化是所有硬件都可以benefit的。
	- ![](../assets/erpg3NiPiT.png)
- AI compiler 的前端和后端分别有什么作用？ #ques1 #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:13:00.421Z
  card-last-reviewed:: 2023-01-28T15:13:00.421Z
  card-last-score:: 5
	- Frontend:
		- take不同的框架的输入，包括tensorflow，pytorch等等。去做一些图级别上的优化。
	- Backend
		- 将high level IR转化为low level IR。
	- ![](../assets/DCWWPquPmD.png)
- What is [[MLIR]]? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 2
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:14:03.493Z
  card-last-reviewed:: 2023-01-28T15:14:03.493Z
  card-last-score:: 5
	- ![](../assets/1u73OD5iS7.png)
	- MLIR
		- 共享尽可能多的优化pass，不同的地方用一个两个pass来完成优化。
- What is the [[Operation in MLIR]]? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:12:17.128Z
  card-last-reviewed:: 2023-01-28T15:12:17.128Z
  card-last-score:: 5
	- MLIR 中operation类似于函数的概念
	- ![](../assets/i4AhVcDwuc.png)
- What is [[Dialect in MLIR]]? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:10:44.185Z
  card-last-reviewed:: 2023-01-28T15:10:44.185Z
  card-last-score:: 5
	- MLIR中Dialect类似于库的概念
	- ![](../assets/bbaDGCLYAB.png)
- What is the difference between IR and Dialect in MLIR? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:13:49.230Z
  card-last-reviewed:: 2023-01-28T15:13:49.230Z
  card-last-score:: 5
	- IR 相当于人说出来的话，Dialect相当于语言。
	- IR是Dialect的具象化。
- What is Region and Block in MLIR? #ques1 #card
  card-last-interval:: 4
  card-repeats:: 2
  card-ease-factor:: 2.46
  card-next-schedule:: 2023-02-01T15:14:09.071Z
  card-last-reviewed:: 2023-01-28T15:14:09.072Z
  card-last-score:: 5
	- ![](../assets/Jl80FJ1owx.png)