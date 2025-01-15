- ## 1. schedule all possible copy out operations
	- ### 1-1. schedule active copy out ops
	  id:: 62331a49-f19e-4569-995c-fc6a5943d276
		- copy out operations
		- `isCopyOutOp`
			-
		- ```c++
		      for (auto& readyOp : _activeComputeOps) {
		          if (isReadyComputeOperationSchedulable(readyOp.first) && isCopyOutOp(readyOp.first)) {
		              _log.trace("Scheduling active copy-out op: '{0}'", readyOp.first);
		              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
		              scheduledActiveOps.push_back(readyOp);
		          }
		      }
		  ```
	- ### 1-2. schedule ready copy out ops
	- ### 1-3. schedule operation not belonging to main network compute chain
		- Scheduling such operations can only happen once all input dependencies (both data and compute ops) have already been executed. This is different to standard compute op which as part of its scheduling can force scheduling of needed data ops
- ## 2. schedule a true compute operation
	- ### 2-1. schedule active op that fit in CMX
	- ### 2-2. schedule ready compute op
- ## 3. update ready lists by removing scheduled ops
	-