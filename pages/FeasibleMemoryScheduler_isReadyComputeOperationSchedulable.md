title:: FeasibleMemoryScheduler::isReadyComputeOperationSchedulable
- ## What does this function do?
	- In general
		- return whether or not can allocation the buffer needed by current compute operation
	- retrieve op demand list - input ops
		- `auto usedBuffers = getNonAliveBuffersUsedByOperation(opIdx);`
			- 1. get `NonAlive Buffers` using [[FeasibleMemoryScheduler::getNonAliveBuffersUsedByOperation]]
			- 2. 这些Buffer需要申请CMX上的存储空间。
		- `auto demandList = getNonEmptyOpDemandList(opIdx, usedBuffers);`
			- 调用[[FeasibleMemoryScheduler::getNonEmptyOpDemandList]]来获取输入的待分配buffer。
	- retrieve operation's buffers that need allocation
		- ```c++
		  // retrieve operation's buffers that need allocation
		      for (auto val : getNonAliveBuffersUsedByOperation(opIdx)) {
		          buffersNeedingAllocation.insert(val);
		      }
		  ```
	- retrieve operation input's buffers
		- ```c++
		      for (auto inputIdx : demandList) {
		          for (auto val : getNonAliveBuffersUsedByOperation(inputIdx)) {
		              buffersNeedingAllocation.insert(val);
		          }
		      }
		  ```
	- sort to minimize fragmentation
		- `auto sortedBuffers = sortUsedBuffers(buffersNeedingAllocation);`
	- return: are resources available and can be allocated
		- ```c++
		  // are resources available and can be allocated
		      return _scan.canAlloc(sortedBuffers);
		  ```
	-