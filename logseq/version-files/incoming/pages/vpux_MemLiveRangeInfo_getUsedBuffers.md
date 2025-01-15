title:: vpux::MemLiveRangeInfo::getUsedBuffers

- ## Questions
- [[Alias in vpux compiler]]指的是什么？ #card #vpux
  card-last-interval:: 4.14
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2024-03-02T15:02:36.841Z
  card-last-reviewed:: 2024-02-27T12:02:36.841Z
  card-last-score:: 5
  collapsed:: true
	- root buffer 就是memref.alloc.outputs_0
	- alias就是%57
	- alias主要是因为有memref.alloc()
- [[Buffer in vpux compiler]]里面指的是什么？ #card #vpux
	- buffer表示一个数据块
		- 例如，%56是memref alloc之后的输出buffer，%57同样表示这个输出块，因为mlir在表示function的时候必须要有输出
		- deallocation在alias列表中的最后一个alias执行完成之后释放空间。
	- ![image.png](../assets/image_1648428417215_0.png)
- ## What does this function do?
-