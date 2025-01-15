title:: 领域编译器发展的前世今生 • 面向ai的编译技术 (highlights)
author:: [[张朔铭]]
full-title:: "领域编译器发展的前世今生 • 面向ai的编译技术"
category:: #articles
url:: http://mp.weixin.qq.com/s?__biz=MzI3MDQ2MjA3OA==&mid=2247486033&idx=1&sn=51f28b71c78a4e328d31ebf54b138a65&chksm=ead1f740dda67e56e96e24f59b8978cb6c247f65ec06e7ca62c30855c72c7ab508758caa1d93#rd
- Highlights first synced by [[Readwise]] [[Feb 7th, 2023]]
	- 为了让开发者使用方便，框架前端(图层)会尽量对Tensor计算进行抽象封装，开发者只要关注逻辑意义上的模型和算子；而在后端算子层的性能优化时，又可以打破算子的边界，从更细粒度的循环调度等维度，结合不同的硬件特点完成优化。这种图算分离的解耦设计大大简化了AI复杂系统的设计，因此，多层IR设计无疑是较好的选择，目前的主流IR设计也是分为图（TVM Relay，XLA HLO，MindSpore MindIR等）和算子（TVM tir，XLA LLO，MindSpore AKG等）两层。 ([View Highlight](https://instapaper.com/read/1576888935/21872906))