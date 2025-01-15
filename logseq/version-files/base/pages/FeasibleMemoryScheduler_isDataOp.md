title:: FeasibleMemoryScheduler::isDataOp
- ## What does this function do?
	- **CopyOp** can be placed directly in **async exec op** or wrapped with **NCEClusterTiling**
	- What is a data op:
		- ==Operations moving data to CMX are considered data ops==. ==All others are considered compute operations.== This distinguishment is needed to balance CMX memory space and not to fill CMX space with only data operations resulting in not being able to fit the compute operation. ==Data operations will only be scheduled when needed by the compute operation so that the CMX space can be freed as soon as possible.==
	- How to check if a CopyOp is a data op?
		- DMA from DDR to NN_CMX
			- If [[_memSpace]] is the dst space of copyOp and not the dst space of copyOp.
			- ```c++
			          if (copyOp) {
			              // DMA from DDR to NN_CMX
			              auto srcMemSpace = copyOp.input().getType().cast<vpux::NDTypeInterface>().getMemSpace();
			              auto dstMemSpace = copyOp.output().getType().cast<vpux::NDTypeInterface>().getMemSpace();
			              return (_memSpace == dstMemSpace && _memSpace != srcMemSpace);
			          }
			  ```
- ## Questions
	- How to get operation from opIdx?
		- `auto op = _depsInfo.getExecuteOpAtIndex(opIdx);`
			- Get [[_allExecOps]] of opIdx
	- How to get current operation name from opIdx?
		- ```C++
		      auto op = _depsInfo.getExecuteOpAtIndex(opIdx);
		      auto curTaskName = stringifyLocation(op->getLoc());
		  ```