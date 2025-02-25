- https://ai.facebook.com/blog/gpu-inference-engine-nvidia-amd-open-source/
- ## AIT:
	- AITemplate is a Python framework that transforms AI models into high-performance C++ GPU template code for accelerating inference.
	- ### Structure:
		- #### frontend:
			- Perform various graph transformations to optimize the graph (python)
			- ```python
			      """Applies graph optimizations, including
			      - fuse permute and bmm
			      - fuse permute and gemm
			      - transform odd alignment
			      - fuse conv and elementwise
			      - fuse gemm and elementwise
			      - fuse elementwise ops
			      - fuse parallel gemms
			      - fuse group ops
			      - transform special ops
			      - transform strided ops
			      - transform memory ops
			      - apply padding
			  
			  ```
		- #### Backend:
			- A back-end layer, where we generate C++ kernel templates for the GPU target
		- #### How this works:
			- AITemplate first runs profiling to find the best kernel configuration in Python, and then renders the Jinja2 template into C++ code.
	- ### Benefits:
		- AIT is a unified inference system with separate acceleration back ends for both AMD and NVIDIA GPU hardware.
		- AIT is much more performant than pytorch with eager mode
			- ![image.png](../assets/image_1674958003958_0.png){:height 442, :width 796}
		- AIT maintains a minimal dependency on external libraries. The AI model is compiled into a self-contained binary without dependencies.
	- ### Drawback:
		- 需要引入一套新的Python建模前端来对模型进行改写是AITemplate这个项目相较其他existing项目最大差异化的地方
		- 依赖于前端提供的一些信息来指导后端的kernel generation或fusion策略，在更换前端以后，就可能带来功能性能的regression
		- 现在的图优化pass里，pattern matching的味道还是有些过于重了，光是conv的fusion就定义了差不多十个pattern，这种作法，如果再加上一些类似于跳边的额外数据输入的变种，会导致项目比较难以维护。
	- ### Algorithm:
		- #### advanced kernel fusion
			- an optimization method that merges multiple kernels into a single kernel to run them more efficiently
				- ![image.png](../assets/image_1674958212997_0.png){:height 446, :width 790}
			- ##### vertical fusion:
				- fuse chains of operations together, including:
					- GEMM and element-wise fusions through CUTLASS and Composable Kernels epilogue fusion
					- GEMM and permute fusions for transformer multihead attention blocks
					- Fusion of memory operations, such as split, slice, and concatenate, with other ops to reduce memory bandwidth via Tensor Accessors
			- ##### horizontal fusion:
				- fuse parallel operations with no dependency together into a single grouped op
			- ##### memory fusion:
				- fuse memory movement ops and computation-intensive operations together
		- #### advanced optimizations for transformer blocks
	- What is python jinja2? #card
	  collapsed:: true
		- https://www.educative.io/answers/what-are-jinja2-templates
		- Jinja, also commonly referred to as "[Jinja2](https://jinja.palletsprojects.com/en/3.0.x/templates/)" to specify the newest release version, is a Python[template engine](https://www.fullstackpython.com/template-engines.html)used to create HTML, XML or other markup formats that are returned to the user via an HTTP response.
		- ![image.png](../assets/image_1674958661388_0.png)