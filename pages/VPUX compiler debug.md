- #vpux
- ```
  	1. How to export log from one pass during vpux compilation 
  	 export IE_VPUX_LOG_FILTER=feasible-allocation
  	 export IE_VPUX_IR_PRINTING_FILTER=tiling
  	
  	 export IE_VPUX_LOG_FILTER=convert-IE-to-VPU-NCE
  	 export IE_VPUX_IR_PRINTING_FILTER=convert-IE-to-VPU-NCE
  	
  	 export IE_VPUX_LOG_FILTER=fuse-mempermute-with-reshape
  	
  	2. Dump dot out 
  
        pm.addPass(vpux::createPrintDotPass("./output/init.dot"));
      
  	3. LLVM  output 
        std::cout<<llvm::formatv("{0} {1}", op->getName(), op->getLoc()).str()<<std::endl;
  ```