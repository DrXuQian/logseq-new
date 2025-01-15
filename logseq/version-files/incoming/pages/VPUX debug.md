- VPUX Debugging:
	- Dump dot from a pass:
		- `export IE_NPU_PRINT_DOT="output=output/debug.dot pass=roll-back-tiling-strategy"`
		- `dot -Tsvg output/debug.dot -o output/debug.dot.svg`
	- Load json strategy:
		- 在compile_tool的时候，MTL.conf文件里面加一个变量 NPU_COMPILATION_MODE_PARAMS **read-strategy-from-json=true** write-strategy-to-json=true
		- 运行compile_tool的路径下面新建一个strategy_in.json的文件把想要manual设置的strategy写上去就行了
	- Mtl.conf
		- ```
		  LOG_LEVEL LOG_INFO
		  NPU_COMPILER_TYPE MLIR
		  NPU_COMPILATION_MODE_PARAMS simple-schedule=true reduce-parallel-control-flows=true write-strategy-to-json=true read-strategy-from-json=true enable-schedule-trace=true enable-activation-sparsity=auto
		  ```