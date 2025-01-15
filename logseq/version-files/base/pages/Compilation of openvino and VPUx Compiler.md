- #vpux
- Build Openvino
	- ```
	  export OPENVINO_HOME='path to you openvino folder'
	  mkdir -p $OPENVINO_HOME/build-x86_64
	  
	  #!/bin/bash
	  set -e
	  git submodule deinit --all -f
	  git submodule sync
	  git submodule update --init --recursive
	  cd $OPENVINO_HOME/build-x86_64
	  rm -rf *
	  cmake -D CMAKE_BUILD_TYPE=Release -D ENABLE_TESTS=ON -D ENABLE_FUNCTIONAL_TESTS=ON ..
	  # if get cldnn error, use -DENABLE_CLDNN=OFF
	  make -j10
	  ```
- Build VPUx compiler
	- ```
	  mkdir -p $KMB_PLUGIN_HOME/build-x86_64
	  
	  git submodule deinit --all -f
	  git submodule sync
	  git submodule update --init --recursive
	  
	  rm -fr $KMB_PLUGIN_HOME/build-x86_64
	  mkdir -p $KMB_PLUGIN_HOME/build-x86_64
	  cd $KMB_PLUGIN_HOME/build-x86_64
	  
	  cmake -DENABLE_TESTS=ON -DENABLE_FUNCTIONAL_TESTS=OFF -DCMAKE_BUILD_TYPE=Release -DInferenceEngineDeveloperPackage_DIR=$OPENVINO_HOME/build-x86_64 -DENABLE_MODELS=OFF -DENABLE_VALIDATION_SET=OFF -DENABLE_DEVELOPER_BUILD=ON -DENABLE_CLANG_FORMAT=ON -DCLANG_FORMAT=/usr/bin/clang-format-9 ..
	  make -j10
	  ```
- 编译过程中遇到的一些问题
	- Ubuntu版本从16升级到了18.04，这个可以online升级
	- 遇到了proxy的错误问题，导致conda无法下载数据：
		- ```
		  export https_proxy=http://child-prc.intel.com:913
		  ```
		- 注意https_proxy等于http的proxy地址。
		- 将proxy配置到/etc/enviroment里面
	- 遇到了ssh配置的问题：
		- github 1source配置网址：
			- https://1source.intel.com/onboard
		- gitlab icv配置：
			- https://gitlab-icv.inn.intel.com/-/profile/keys
	- 改变文件的权限，比如`chmod -R 777 ./*`会导致文件在git中被标记成已经修改，应该和git判定文件的内容相关，不能随便修改这个特质。
- 验证安装正确性
	- 1. Compile tool
		- ```
		  ./compile_tool -m /home/qianxu/Desktop/ai.vpu.arch.flex.nb_contrib.nbgraph/binary/nbgraph-bin-1/fp16/squeezenet1.1.xml -o mcm.blob -d VPUX.3700 -ip FP16 -op FP16
		  ```
		- 目前还有问题：
		- ```
		  OpenVINO Runtime version ......... 2022.1.0
		  Build ........... custom_master_18035209a0b26c722541a65a112212afcae29d8b
		  Network inputs:
		      data : f16 / [...]
		  Network outputs:
		      prob/sink_port_0 : f16 / [...]
		  Device with "VPUX" name is not registered in the InferenceEngine
		  
		  ```
	- 2. vpux-opt
	- ```
	  vpux-opt --split-input-file --prefetch-tiling --canonicalize %s
	  ```
	- 3. How to debug
		- `compile_tool`
		- Build Openvino
			- ```
			  export OPENVINO_HOME='path to you openvino folder'
			  mkdir -p $OPENVINO_HOME/debug-x86_64
			  
			  #!/bin/bash
			  set -e
			  git submodule deinit --all -f
			  git submodule sync
			  git submodule update --init --recursive
			  cd $OPENVINO_HOME/debug-x86_64
			  rm -rf *
			    cmake -D CMAKE_BUILD_TYPE=Debug -D ENABLE_TESTS=ON -D ENABLE_FUNCTIONAL_TESTS=ON ..
			  # if get cldnn error, use -DENABLE_CLDNN=OFF
			  make -j10
			  ```
		- Build VPUx compiler
			- ```
			  export KMB_PLUGIN_HOME='path to you kmb plugin folder'
			  mkdir -p $KMB_PLUGIN_HOME/debug-x86_64
			  
			  git submodule deinit --all -f
			  git submodule sync
			  git submodule update --init --recursive
			  
			  rm -fr $KMB_PLUGIN_HOME/debug-x86_64
			  mkdir -p $KMB_PLUGIN_HOME/debug-x86_64
			  cd $KMB_PLUGIN_HOME/debug-x86_64
			  
			  cmake -DENABLE_TESTS=ON -DENABLE_FUNCTIONAL_TESTS=OFF -DCMAKE_BUILD_TYPE=Debug -DInferenceEngineDeveloperPackage_DIR=$OPENVINO_HOME/debug-x86_64 -DENABLE_MODELS=OFF -DENABLE_VALIDATION_SET=OFF -DENABLE_DEVELOPER_BUILD=ON -DENABLE_CLANG_FORMAT=ON -DCLANG_FORMAT=/usr/bin/clang-format-9 ..
			  make -j10
			  ```
		-