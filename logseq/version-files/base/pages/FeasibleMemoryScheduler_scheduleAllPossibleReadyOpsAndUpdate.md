title:: FeasibleMemoryScheduler::scheduleAllPossibleReadyOpsAndUpdate
- ## What does this function do?
	- 1. schedule all possible copy out operations
		- 1-1. schedule active copy out ops
			- ```c++
			  for (auto& readyOp : _activeComputeOps) {
			          if (isReadyComputeOperationSchedulable(readyOp.first) && isCopyOutOp(readyOp.first)) {
			              _log.trace("Scheduling active copy-out op: '{0}'", readyOp.first);
			              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
			              scheduledActiveOps.push_back(readyOp);
			          }
			      }
			  ```
		- 1-2. schedule ready copy out ops
			- ```c++
			  for (auto& readyOp : _readyComputeOps) {
			          if (isReadyComputeOperationSchedulable(readyOp.first) && isCopyOutOp(readyOp.first)) {
			              _log.trace("Scheduling ready copy-out op: '{0}'", readyOp.first);
			              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
			              scheduledReadyOps.push_back(readyOp);
			          }
			      }
			  ```
			- [[FeasibleMemoryScheduler::isCopyOutOp]]
			- [[FeasibleMemoryScheduler::isReadyComputeOperationSchedulable]]
			- [[FeasibleMemoryScheduler::scheduleComputeOp]]
		- 1-3. schedule operation not belonging to main network compute chain
	- 2. schedule a true compute operation
		- 2-1. schedule active op that fit in CMX
			- not a copy out op
				- ```c++
				      for (auto& readyOp : _activeComputeOps) {
				          if (isReadyComputeOperationSchedulable(readyOp.first) && !isCopyOutOp(readyOp.first) && !trueComputeScheduled) {
				              _log.trace("Scheduling active compute op: '{0}'", readyOp.first);
				              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
				              scheduledActiveOps.push_back(readyOp);
				              trueComputeScheduled = true;
				          }
				      }
				  ```
		- 2-2. schedule ready compute op
			- ```c++
			  for (auto& readyOp : _readyComputeOps) {
			          if (isReadyComputeOperationSchedulable(readyOp.first) && !isCopyOutOp(readyOp.first) && !trueComputeScheduled) {
			              _log.trace("Scheduling ready compute op: '{0}'", readyOp.first);
			              computeOpStartTime = std::max(computeOpStartTime, scheduleComputeOp(readyOp.first));
			              scheduledReadyOps.push_back(readyOp);
			              trueComputeScheduled = true;
			          }
			      }
			  ```
	- 3. update ready lists by removing scheduled ops
		- ```c++
		  for (auto scheduledOp : scheduledActiveOps) {
		          _activeComputeOps.erase(scheduledOp);
		      }
		      for (auto scheduledOp : scheduledReadyOps) {
		          _readyComputeOps.erase(scheduledOp);
		      }
		      for (auto scheduledOp : scheduledNonComputeChainOps) {
		          _nonComputeChainOps.erase(scheduledOp);
		      }
		  ```