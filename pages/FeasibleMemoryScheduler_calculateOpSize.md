title:: FeasibleMemoryScheduler::calculateOpSize
## What does this function do?
	- Get inputs and outputs for this operation
	- Add the input size and output size to **opsize** if input or output in memory (CMX)
	- return **opsize**
- ## Questions
	- How to get bodyBlock from operator?
		- `auto* bodyBlock = &_depsInfo.getExecuteOpAtIndex(opIdx).body().front();`
	- How to get operator outputs and inputs?
		- `auto outputs = mlir::dyn_cast<IERT::LayerOpInterface>(op).getOutputs();`
		- `auto inputs = mlir::dyn_cast<IERT::LayerOpInterface>(op).getInputs();`
	- How to get input/output memory type?
		- `const auto type = output.getType().dyn_cast<vpux::NDTypeInterface>();`
		- `type.getMemSpace() != _memSpace`
	- How to get size from output?
		- `_scan.handler().getSize(output)`