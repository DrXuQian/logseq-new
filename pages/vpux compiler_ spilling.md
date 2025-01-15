title:: vpux compiler: spilling
- alias:: VPUXCompiler spilling
- 问题：
	- `spill`的时候需要对图进行修改，并且这个修改会影响后面的`dataop`，如何实现的？
- Affirmations
	- ```c++
	          bool isSpillWrite() const {
	              return (opType_ == EOpType::IMPLICIT_OP_WRITE);
	          }
	          bool isSpillRead() const {
	              return (opType_ == EOpType::IMPLICIT_OP_READ);
	          }
	          bool isPrefetched() const {
	              return (opType_ == EOpType::ORIGINAL_PREFETCHED_OP);
	          }
	          bool isOriginalOp() const {
	              return (opType_ == EOpType::ORIGINAL_OP) || (opType_ == EOpType::ORIGINAL_PREFETCHED_OP);
	          }
	  ```
- Spill 发生的位置
	- `forceScheduleActiveOpEviction`
		- `nextSchedulableOp`
collapsed:: true
			- `init`
				- `generateSchedule`
					- `feasible_allocation` pass
	- `scheduleSpilledOpBuffer`
		- `allocateBuffersAndInputOps`
			- `scheduleComputeOp`
collapsed:: true
				- `scheduleAllPossibleReadyOpsAndUpdate`
					- `init`
						- `generateSchedule`
							- `feasible_allocation` pass
					- `nextSchedulableOp`
						- `init`
							- `generateSchedule`
								- `feasible_allocation` pass
			- `schedulePrefetchedDataOps`
collapsed:: true
				- `nextSchedulableOp`
					- `init`
						- `generateSchedule`
							- `feasible_allocation` pass
	-