title:: vpux::isBufAllocOp

- ## What does this function do?
	- return True if the result of this op is `isa<mlir::MemRefType, VPUIP::DistributedBufferType>`
	- 这个op还要满足operands数量大于0，并且只有一个输出