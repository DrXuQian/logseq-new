- [[vpux compiler : prefetching]]
	- `src/vpux_compiler/src/core/feasible_memory_scheduler.cpp`
	- ## Feasible Memory Scheduler
	- This class will try to produce a feasible memory schedule based on the dependency map provided from `AsyncDepsInfo` and use the `LinearScan class` to allocate the resources.
	- ==Data and Compute ops, where Data ops are operations moving data to CMX, are distinguished in order to follow the scheduling of Compute ops along with their dependencies (Data ops).== This optimizes CMX usage, and allows for feasible CMX schedule to be generated.
	- **The graph is iterated topologically based on the dependencies from input to output(s).**
	- In `init()` the input will be considered as a compute operation, this will be the first ready compute operation.
	- In `nextSchedulableOp()` there are two possible scenarios:
		- 1. Scheduling the next earliest operation from the start time heap, and adding it to the op output table.
		  2. Unscheduling operations: freeing CMX space and updating dependencies, creating new ready operations which will be allocated at the next time slot.
	- ### `SafeRunOnModule()`
		- ```C++
		      // linear scan
		      auto available = IE::getAvailableMemory(module, _memSpace.getFullReference());
		      const auto maxSize = available.size();
		      const uint64_t alignment = 64;
		  ```
			- 获取可用的memory大小，`maxSize = 917504 = 896 * 1024 = 0.875 M`
		- ```c++
		      LinearScan<mlir::Value, LinearScanHandler> scan(maxSize.count(), alignment);
		      auto& aliasesInfo = getChildAnalysis<AliasesInfo>(netFunc);
		      auto& liveRangeInfo = getChildAnalysis<MemLiveRangeInfo>(netFunc);
		      auto& depsInfo = getChildAnalysis<AsyncDepsInfo>(netFunc);
		  ```
			- 创建一个`LinearScan`的instance
			- 获取`AsyncDepsInfo`和`liveRangeInfo`，后面会使用
		- ```c++
		      // Copy classes for iteration with prefetch edges, as for prefetching
		      // scheduler will run twice and first iteration is used to gather information
		      // about the schedule and second one will perform the final allocation
		      auto prefetchScan = scan;
		      auto prefetchLiveRangeInfo = liveRangeInfo;
		  ```
			- `prefetching scheduler` will run twice
				- first iteration is used to gather information about the schedule
				- second one perform the final allocation
			- 疑问：这个不是跟feasible mem allocation一起做的吗?
				- 不是
			- 从图上来看`prefetching scheduler`:
			  collapsed:: true
				- Before:
					- ![precreateFeasibleAllocationPass.dot.svg](../assets/precreateFeasibleAllocationPass.dot_1647677953072_0.svg)
				- After:
					- ![createFeasibleAllocationPass.dot.svg](../assets/createFeasibleAllocationPass.dot_1647677971046_0.svg)
				- For `conv2d_1`:
					- Before:
						- ![image.png](../assets/image_1647424510830_0.png)
					- After:
						- Indegree:
							- ![image.png](../assets/image_1647424597386_0.png)
						- Outdegree:
							- Same as before, `conv2d_1`
		- ```c++
		      // feasible memory scheduler - list scheduler
		      FeasibleMemoryScheduler scheduler(_memSpace, liveRangeInfo, depsInfo, aliasesInfo, _log, scan);
		  
		      // 1. initial schedule
		      auto scheduledOps = scheduler.generateSchedule();
		  
		  ```
			- 生成初始的memory allocation
			- `scheduler.generateSchedule()`
				- `init();`
					- ```c++
					  bool FeasibleMemoryScheduler::init() {
					      _log.trace("Feasible Memory Scheduler init()");
					      _currentTime = 1;
					      _depsInfo.buildConsMap();
					  
					      // compute op in/out degree
					      _inDegreeTable = _depsInfo.calculateOpInDegreeTable();
					      _outDegreeTable = _depsInfo.calculateOpOutDegreeTable();
					  
					      // retrieve output ops (ops with no out-degree)
					      for (auto& entry : _outDegreeTable) {
					          if (entry.second == 0) {
					              _outputOps.insert(entry.first);
					          }
					      }
					  
					      clearLists();
					      // TODO: check if input is dag
					      getReadyDataList();
					      getReadyComputeList();
					      scheduleAllPossibleReadyOpsAndUpdate();
					      nextSchedulableOp();
					  
					      return true;
					  }
					  ```
						- 更新`_depsMap`, `_consumerMap`, `_inDegreeTable`, `_outDegreeTable`
							- `_depsMap`包含这个operation的producer的信息
							- `_consumerMap`包含这个operation的consumer的信息
							- [[Vpux Compiler: build depsMap and consumerMap]]
						- 生成`_outputOps`
							- 输出节点的出度为0
						- `clearLists`
							- `_readyComputeOps`所有的input operation都是ready operations
							- `_readyDataOps`在CMX上的数据
							- `_activeComputeOps`至少有一个输入节点没有ready的op
							- ```c++
							      _readyComputeOps.clear();   // ready operations with no active input
							      _readyDataOps.clear();      // ready data inputs (->CMX)
							      _activeComputeOps.clear();  // compute operations with at least one active input
							  ```
						- `getReadyDataList`
							- 查看已经ready的数据，也就是入度为0的`dataOp`, 如果当前的`dataOp`被放到了`_readyDataOps`，中，那么这个op对应的consumer需要在`reduceInDegreeOfAdjacentOperations`函数中对入度-1
							- ```c++
							  void FeasibleMemoryScheduler::getReadyDataList() {
							      // populate ready data ops
							      for (auto& entry : _inDegreeTable) {
							          if (entry.second == 0 && isDataOp(entry.first) && !isNonComputeChainOp(entry.first)) {
							              vpux::AddressType opSize = calculateOpSize(entry.first);
							              _readyDataOps.insert(std::make_pair(entry.first, opSize));
							              // reduce indegree of op consumers
							              auto newOps = reduceInDegreeOfAdjacentOperations(entry.first);
							              if (!newOps.empty()) {
							                  for (auto& op : newOps) {
							                      opSize = calculateOpSize(op);
							                      _nonComputeChainOps.insert(std::make_pair(op, opSize));
							                  }
							              }
							          }
							      }
							  }
							  
							  ```
						- `getReadyComputeList`
							- 对于`computeOp`也是一样的，并且对于我的测试graph，`getReadyDataList`和`getReadyComputeList`都只进入了一次内部循环
							- ```c++
							  void FeasibleMemoryScheduler::getReadyComputeList() {
							      // populate ready compute ops
							      for (auto& entry : _inDegreeTable) {
							          if (entry.second == 0 && !isDataOp(entry.first) && !isNonComputeChainOp(entry.first)) {
							              vpux::AddressType opSize = calculateOpSize(entry.first);
							              _readyComputeOps.insert(std::make_pair(entry.first, opSize));
							          }
							      }
							  }
							  ```
						- `scheduleAllPossibleReadyOpsAndUpdate`
							- 2-2. schedule ready compute op, erase scheduled ready ops from `_readyComputeOps`
								- ```c++
								      //      2-2. schedule ready compute op
								      for (auto& readyOp : _readyComputeOps) {
								          if (isReadyComputeOperationSchedulable(readyOp.first) && !isCopyOutOp(readyOp.first) && !trueComputeScheduled) {
								              _log.trace("Scheduling ready compute op: '{0}'", readyOp.first);
								              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
								              scheduledReadyOps.push_back(readyOp);
								              trueComputeScheduled = true;
								          }
								      }
								  ```
						- `nextSchedulableOp`
							- scheduling loop, loop until all output ops are scheduled
								- DONE `populateScheduledOps`
									- ==Note: `_scheduledOps`是在这里更新的==
									- ```c++
									      // populate the struct fields
									      ScheduledOpInfo scheduled;
									      scheduled.op_ = scheduledOp.op_;
									      scheduled.opType_ = scheduledOp.opType_;
									      scheduled.time_ = scheduledOp.time_;
									      scheduled.resourceInfo_ = intervals;
									      scheduled.isDataOp_ = isDataOp(scheduledOp.op_);
									      scheduled.freeCmx_ = _scan.totalFreeSize();
									      _scheduledOps.push_back(scheduled);
									  ```
								- DONE What is the time heap in this function?
							- pop compute operator from time heap
		- ```c++
		      // 2. prefetching
		      // 2.1. optimization for inital schedule - generating prefetch edges
		      PrefetchEdgeGenerator PrefetchEdgeGenerator(scheduledOps, depsInfo);
		      auto prefetchEdges = PrefetchEdgeGenerator.generatePrefetchEdges();
		  
		  ```
			- `generatePrefetchEdges`
				- 从`_scheduledOps`中获取`computeOp`，放到`_executedOps`中
				-
- Questions:
	- What is [[mlir::MLIRContext]]? #card
	- What is [[mlir::ModuleOp]]? #card
	- What is [[mlir::OpTrait]]? #card
-