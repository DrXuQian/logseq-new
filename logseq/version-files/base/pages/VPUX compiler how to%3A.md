title:: VPUX compiler how to:
- #vpux
- Compile a xml file through VPUX compiler
	- MTL: VPUX.3720
	- ```
	   ../openvino/bin/intel64/Release/compile_tool -m ./elevoc/model-426000-0.052085492461919784_edit.xml -o elevoc.blob -d  VPUX.3720
	  ```
- Covert blob to json
	- ~/repo/applications.ai.vpu-accelerators.vpux-plugin/thirdparty/graphFile-schema/flatbuffers/flatc --json --strict-json ~/repo/applications.ai.vpu-accelerators.vpux-plugin/thirdparty/graphFile-schema/src/schema/graphfile.fbs -- resnet-50-pytorch-cut-sok.blob
- flatc add to the system PATH
	- ```
	  sudo ln -s /home/qianxu/Desktop/applications.ai.vpu-accelerators.vpux-plugin/thirdparty/graphFile-schema/flatbuffers/flatc /usr/local/bin/flatc
	  ```
- How to print the trace within a certain pass:
	- pass的名字可以在这里看src/vpux_compiler/docs/chapters/generated/dialect/VPU/_passes.md
	- ```
	  export IE_VPUX_LOG_FILTER=cmx-concat
	  ```