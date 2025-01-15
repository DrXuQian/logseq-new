- From Yingyue:
	- ```
	  PLUGIN:
	  commit efe77e07f8f733c6881f5d0fef57636754ddf1c8 (tag: ci_tag_vpux_rc_20230820_2101, origin/releases/2023/0)
	  Author: Mateusz Darecki <mateusz.darecki@intel.com>
	  Date:   Sun Aug 20 19:00:56 2023 +0200
	      Update SW kernel task naming when unrolling not multiclustered SW task. Needed for profiling postprocessing with schedule trace feature (#5667)
	  
	  OV:
	  commit a6351294e77119fa470768dc7e4eb401f0b9be12 (HEAD)
	  Author: Stefania Hergane <stefania-persida.hergane@intel.com>
	  Date:   Tue Aug 8 23:19:25 2023 +0300
	  
	  
	  git submodule deinit --all -f
	  git submodule sync
	  git submodule update --init --recursive
	   
	  
	  rm -fr $OPENVINO_HOME/build-x86_64
	  mkdir -p $OPENVINO_HOME/build-x86_64
	  cd $OPENVINO_HOME/build-x86_64
	  ```
	- ```
	  # OV我用的这个
	  cmake -DCMAKE_BUILD_TYPE=Release -DENABLE_INTEL_GNA=ON -DENABLE_TESTS=ON -DENABLE_FUNCTIONAL_TESTS=ON -DENABLE_INTEL_GPU=OFF -DENABLE_PLUGINS_XML=ON -DENABLE_OV_TF_LITE_FRONTEND=OFF .. && make -j48
	  
	  # VPUX
	  # cmake -DENABLE_TESTS=OFF -DENABLE_FUNCTIONAL_TESTS=OFF  -DCMAKE_BUILD_TYPE=Release -DInferenceEngineDeveloperPackage_DIR=$OPENVINO_HOME/build-x86_64 -DENABLE_MODELS=OFF -DENABLE_VALIDATION_SET=OFF -DENABLE_DEVELOPER_BUILD=ON -DENABLE_CLANG_FORMAT=ON -DCLANG_FORMAT=/usr/bin/clang-format-9 -DENABLE_PLUGINS_XML=ON -DENABLE_OV_TF_LITE_FRONTEND=OFF .. && make -j48
	  ```