- [[Distributed data parallel]]
- Distribute naive的想法：
	- ![](https://pic2.zhimg.com/80/v2-56eaaa7d65d4c43cc7c490731fca5491_720w.webp)
	- 在做完所有的backward之后同步一下，allreduce拿到自己local的parameter需要的gradient
	- Drawback:
		- allreduce是在backward完全结束之后进行的，导致GPU的计算和通信变成串行操作。
		- 对于每个梯度都需要进行一次allreduce，当梯度较小且模型又比较大时，all reduce的通信性能会变得很差，因为小数据量的通信无法充分利用通信带宽。
- Pytorch solution：
	- https://user-images.githubusercontent.com/16999635/72401724-d296d880-371a-11ea-90ab-737f86543df9.png
	- ![](https://pic1.zhimg.com/80/v2-37c8f89579088224731cfc60fa5377f8_720w.webp)
	- 首先把gradient分成不同的桶，每个桶对应了多个gradient，这样就解决了梯度小的时候用不满带宽的问题
	  logseq.order-list-type:: number
	- 然后当对应的桶里面的gradient ready之后就可以进行通信，这样communication和compute就pipeline了
	  logseq.order-list-type:: number
-