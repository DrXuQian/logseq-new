title:: FeasibleMemoryScheduler::init

- ## What does this function do?
	- 1. Set [[_currentTime]] to 1
	- 2. [[vpux::AsyncDepsInfo::buildConsMap]] according to [[_depsInfo]]
	- 3.build [[_inDegreeTable]] and [[_outDegreeTable]] according to [[_depsInfo]]
	- 4.build [[_outputOps]] according to [[_outDegreeTable]], add to `_outputOps` if out degree==0
	- 5. clear operator related memory class members
		- [[_readyComputeOps]] [[_readyDataOps]] [[_activeComputeOps]]
	- 6. [[FeasibleMemoryScheduler::getReadyDataList]]
		- 把入度为0的dataop放入[[_readyDataOps]]
	- 7. [[FeasibleMemoryScheduler::getReadyComputeList]]
		- 把入度为0不是dataOp的都放入[[_readyComputeOps]]
		- 初始的时候6，7可能都只有一个ready
	- 8. [[FeasibleMemoryScheduler::scheduleAllPossibleReadyOpsAndUpdate]]
	- 9. [[FeasibleMemoryScheduler::nextSchedulableOp]]
	-