title:: vpu: feasible allocation

- ## Questions:
- How to get `netFunc` and `netOp` from context? #card #vpux
	- ```C++
	      auto module = getOperation();
	  
	      IE::CNNNetworkOp netOp;
	      mlir::FuncOp netFunc;
	      IE::CNNNetworkOp::getFromModule(module, netOp, netFunc);
	  ```
- ## Class Members
	- [[_memSpace]]
		- memory space, which will be allocated
	- [[_liveRangeInfo]]
		- information about op buffers
	- [[_depsInfo]]
		- dependencies of ops
	- [[_aliasInfo]]
		- aliases information for buffers
	- [[_scan]]
		- allocator class
	- [[_startTimeHeap]]
		- heap with earliest operation start time
	- [[_completionTimeHeap]]
		- heap with earlies operation completion time
	- [[_activeComputeOps]]
		- operations with active input
		- compute operations with at least one active input
	- [[_readyComputeOps]]
		- compute operations with 0 in-degree
		- ready operations with no active input
	- [[_readyDataOps]]
		- data operations with 0 in-degree
		- ready data inputs (->CMX)
	- [[_nonComputeChainOps]]
		- operations which do not belong to main compute chain for activations from network input to output. Such operations need to be distinguished from other ops as scheduler is focused on scheduling ops along compute chain. Such operation will only be considered for scheduling once all input dependency data and/or compute ops have been executed
	- [[_inDegreeTable]]
		- operation in-degree, number of incoming edges
	- [[_outDegreeTable]]
		- operation out-degree, number of outgoing edges
	- [[_prefetchEdges]]
		- map storing prefetch edges
	- [[_prefetchDataOps]]
		- unlocked prefetch data ops
	- [[_opOutputTable]]
		- contains scheduled ops along with their status/type
	- [[_opWritingToBuffer]]
		- contains the operation writing to the buffer
	- [[_scheduledOps]]
		- container for the schedule output
	- [[_outputOps]]
		- outputs of the graph
	- [[_currentTime]]
		- schedule time
- ## `SafeRunOnModule()`
	- Initialize feasible memory scheduler class
		- 1. First get the available memory of `_memSpace`, 917504 in this case (0x000E0000)
		- 2. Build a linear scaner based on the mem space and alignment (64).
		- 3. Create `aliasesInfo` and `liveRangeInfo` and `depsInfo`
			- [[AliasesInfo class init]]
			- [[MemLiveRangeInfo class init]]
			- [[AsyncDepsInfo class init]]
		- 4. Copy `scan` and `liveRangeInfo` classes for iteration with prefetch edges as for prefetching scheduler will run **==twice==** and ==first iteration is used to gather information about the schedule and second one will perform the final allocation==
		- 5. Create `FeasibleMemoryScheduler` class named `scheduler`, updated private class members in class (`_scan, _liveRangeInfo, _depsInfo, _aliasesInfo`)
		-
	- Run actual memory allocation
		- 1. initial schedule
			- [[FeasibleMemoryScheduler::generateSchedule]]
-