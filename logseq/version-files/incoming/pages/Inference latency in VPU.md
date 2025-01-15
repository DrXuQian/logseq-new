## Inference latency
	- ### [[First Ever Inference Latency]] for Compiler in Driver
		- First Ever inference Latency = Time to Inference + First Inference
			- Time to inference = Loading model from disk + Loading the plugin + creating an executable network (**compilation + save to cache**) + Preparing IO blob + creating infer request
			- First Inference = fill input + Infer + get results
		- https://docs.intel.com/documents/MovidiusExternal/vpu4/LNL/sw/auto/f2.Detailed_FEIL_flow_.d4bd03.zoom.html
	-