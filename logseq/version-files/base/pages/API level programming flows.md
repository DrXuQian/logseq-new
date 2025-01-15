### [[Level0]] Interface
	- #### [[Level Zero]] Compilation and Submission Flow
		- 编译网络，或者直接load compile好的网络
		- 设置输出输入参数
		- 将图的command放入到command list里面
		- 提交给command queue，然后执行该图的inference
		- ![image.png](../assets/image_1714831334658_0.png)
		- #### Level Zero Compilation and Submission Flow
			- ##### Setting Graph Arguments Using Level Zero
				- While building up the command list for submission, arguments will need to be set for the graph including:
					- Inputs and outputs
					- Weights and other parameters
					- Scratch memory allocation
				- ```c++
				    // set Graph arguments
				    result = zeGraphSetArgumentValue(
				            hGraph,          //handle of the graph object
				            0,               //index of the argument to get properties
				            &input_buffer    //pointer to argument value
				      );
				    if (result != ZE_RESULT_SUCCESS) {
				      throw std::runtime_error("zeGraphSetArgumentValue failed: " +
				                               to_string(result));
				    }
				    LOG_DEBUG << "input buffer set as graph argument";
				  
				      result = zeGraphSetArgumentValue(
				            hGraph,          //handle of the graph object
				            1,               //index of the argument to get properties
				            &output_buffer    //pointer to argument value
				      );
				    if (result != ZE_RESULT_SUCCESS) {
				      throw std::runtime_error("zeGraphSetArgumentValue failed: " +
				                               to_string(result));
				    }
				    LOG_DEBUG << "output buffer set as graph argument";
				  ```
	- #### Level Zero Memory Allocation
		- The memory are all on host DRAM. But the visibility of different data location is different.
			- zeDriverAllocDeviceMem
			- zeDriverAllocHostMem
			- zeDriverAllocSharedMem
				- Memory visible only to the device will typically ==require an additional copy== and is better for data that will be used multiple times This data will not be coherent with the CPU and so it has the advantage ==it doesn't need a snoop to the CPU cache Memory== visible to the device and CPU does not need an extra copy, and is best for data only used once like inputs/outputs.
				- 在讨论内存时，"不需要对CPU缓存进行探查（snoop）"这一优点意味着，当内存数据仅由设备（如GPU）访问，并不可见于CPU时，这些数据在访问时不需要检查CPU的cache来保证数据一致性。在多处理器系统中，为了保证数据一致性，通常需要对各个处理器的cache进行探查，确保数据最新。如果内存区域不与CPU共享，那么设备在访问这些内存时就无需执行这一耗时的探查过程，从而可以提高数据处理的效率。
		- Before inference, need to allocate memory for:
			- inputs/outputs
			- weights
			- scratch memory for intermediate activations
			- ![image.png](../assets/image_1714891706796_0.png){:height 412, :width 783}
		- ![image.png](../assets/image_1714870704486_0.png)
	- #### Level Zero [[Command Lists]]
	  collapsed:: true
		- A command list represents a sequence of commands for execution that is pointed to by an entry in the command queue. The command list will be built by the application based on commands created by the compiler(s) and UMD.
		- 在Level Zero中，"fence"（屏障）是一种同步机制，用于确定命令队列中命令的完成情况。当命令提交到设备上执行时，可以将屏障与这些命令关联，以跟踪它们何时完成执行。一旦与特定屏障关联的命令完成，屏障的状态会更新，可以查询此状态以确保主机和设备之间的同步。这有助于管理依赖关系并确保复杂计算中的正确执行顺序。
	- #### Level Zero Graph Compilation
	  collapsed:: true
		- To support graph level compilation in the Level Zero interface a graph creation step is needed. The compiler is a part of the UMD. The UMD interface forwards the graph data to the compiler, and returns the results. This graph creation step will take in the graph description and weights and will output the compiled graph, optimized weights and any build info from the compiler. Level zero provides two input formats to the compiler, one format is SPIRV, and one is a device native format. We will only support the native format. Different native formats will be supported, and there will be a header in the binary stating what format it is for future expandability.
		- ![image.png](../assets/image_1714891607923_0.png)
- ### [[NPU Plugin]]
	- NPU Plugin is a software library that:
		- Implements the unified OpenVINO Plugin API used to compile and execute neural networks on NPU devices.
		- Uses the graph extension API exposed by the driver to convert the OpenVINO specific representation of the model into a proprietary format. The compiler performs platform specific optimizations in order to efficiently schedule the execution of layers and memory transactions on various NPU hardware submodules.
		- Uses the Level Zero API implemented by the NPU user mode driver (UMD) to execute the model on the device.
	- #### Model compilation
		- NPU plugin implements the OpenVINO Core "compile_model" API that converts the model representation into a proprietary format that can be executed on the NPU device:
		- ```c++
		      ov::CompiledModel compiled_model = core.compile_model(model, "NPU" [, config]);
		  ```
	- #### Model caching
		- First Ever Inference Latency (FEIL): Measures all steps required to compile and execute a model on the device for the first time. It includes model compilation time, the time required to load and initialize the model on the device and the first inference execution.
		- First Inference Latency (FIL): Measures the time required to load and initialize the pre-compiled model on the device and the first inference execution.
		- ##### UMD dynamic model caching
			- cache the model to improve first inference latency. Store the compiled model in cache after first ever compilation based on hash key.
		- ##### OpenVINO model caching
			- can also use openvino internal cache strategy and bypass the UMD cache
	- #### Compiler Adapters
		- ![High Level Design](https://github.com/openvinotoolkit/openvino/raw/master/src/plugins/intel_npu/docs/img/high_level_design.png){:height 549, :width 432}
		- Two additional layers are required to support the compiler from driver:
			- Compiler Adapter - It serializes the OpenVINO internal representation of the model (ov::model) into an in-memory IR that will be provided to the NPU driver
			- VCL - It deserializes the in-memory IR given by the NPU driver and prepares it for the compiler
	- The interface between plugin and driver is based on an in-memory IR to facilitate backward and forward compatibility between two software packages (OpenVINO and NPU driver) that inherently have a different release cadence.
	- #### Model execution
		- NPU plugin will use the Level Zero (L0) API to execute the precompiled model on the NPU Device. The inference is executed as a standard L0 workload by describing the required tasks inside a command list and by submitting the list to the command queue for execution. The plugin will not use the CPU to execute any part of the inference workload. No pre/post processing workloads are executed on the CPU either, the entire inference will be offloaded on the NPU device.
	- #### Inference pipeline
		- The result of the model compilation is represented through a NetworkDescription. This model description is passed by the plugin to the driver to create a level zero graph instance and obtain a graph handle that can later be used to execute multiple inferences in parallel for the same model.
	- #### Memory allocation
		- The set of input and output buffers allocated by the NPU plugin for each inference request can be retrieved using the following API:
			- `ov::Tensor requestTensor = inferRequest.get_tensor(tensor_name);`
		- Once the inference request is created and input/output tensors are prepared for the inference, the execution can be triggered either synchronously or asynchronously:
		- Synchronous execution:
			- ```
			  inferRequest.infer();
			  ```
		- Asynchronous execution using:
			- ```
			  inferRequest.start_async();
			    inferRequest.wait(); // optional, in case user callback is not provided
			  ```
		-
	- #### Call Stack from openvino npu plugin:
		- ```c++
		  CompiledModel::CompiledModel(const std::shared_ptr<const ov::Model>& model,
		                               const std::shared_ptr<const ov::IPlugin>& plugin,
		                               const std::shared_ptr<IDevice>& device,
		                               const ov::SoPtr<ICompiler>& compiler,
		                               const bool profiling,
		                               const Config& config){
		    ...
		    try {
		            _networkPtr = std::make_shared<const NetworkDescription>(compiler->compile(model, config));
		    }
		  }
		  ```
		- ```c++
		  
		  std::shared_ptr<ov::IAsyncInferRequest> CompiledModel::create_infer_request() const {
		  	...
		      if (_executorPtr == nullptr && _device != nullptr) {
		          _executorPtr = _device->createExecutor(_networkPtr, _config);
		      }
		  
		      const std::shared_ptr<SyncInferRequest>& syncInferRequest =
		          _device->createInferRequest(shared_from_this(), _executorPtr, _config);
		      syncInferRequest->initialize_states();
		  
		      return std::make_shared<AsyncInferRequest>(syncInferRequest,
		                                                 get_task_executor(),
		                                                 _resultExecutor,
		                                                 get_callback_executor());
		  }
		  ```
		- ```c++
		  std::shared_ptr<IExecutor> ZeroDevice::createExecutor(
		      const std::shared_ptr<const NetworkDescription>& networkDescription,
		      const Config& config) {
		      OV_ITT_SCOPED_TASK(itt::domains::LevelZeroBackend, "Device::createExecutor");
		      return std::make_shared<ZeroExecutor>(_initStructs, networkDescription, config, _group_ordinal);
		  }
		  ```
		- ```c++
		  class ZeroExecutor final : public IExecutor {
		  public:
		      ZeroExecutor(const std::shared_ptr<const ZeroInitStructsHolder>& initStructs,
		                   const std::shared_ptr<const NetworkDescription>& networkDescription,
		                   const Config& config,
		                   const uint32_t& group_ordinal);
		  	...
		  ```
		- ```c++
		  ZeroInferRequest::ZeroInferRequest(const std::shared_ptr<ZeroInitStructsHolder>& backendPtr,
		                                     const std::shared_ptr<const ICompiledModel>& compiledModel,
		                                     const std::shared_ptr<const IExecutor>& executor,
		                                     const Config& config){
		    ...
		    allocate_tensor(inputName, parameterDescriptor, TensorType::InputOrOutput, inputAllocator);
		    allocate_tensor(outputName, resultDescriptor, TensorType::InputOrOutput, allocator);
		    allocate_tensor(stateName, stateDescriptor, TensorType::State, allocator);
		    /// Construct pipepline
		    _pipeline = makePipeline(_executorPtr,
		                             _config,
		                             _profilingPool,
		                             _profilingQuery,
		                             _npuProfiling,
		                             _copyAllTensors,
		                             _batchSize);
		  }
		  ```
		- ```c++
		  std::unique_ptr<Pipeline> makePipeline(const std::shared_ptr<const IExecutor>& executorPtr,
		                                         const Config& config,
		                                         zeroProfiling::ProfilingPool& profiling_pool,
		                                         zeroProfiling::ProfilingQuery& profiling_query,
		                                         std::shared_ptr<zeroProfiling::NpuInferProfiling> npu_profiling,
		                                         std::unordered_map<std::string, std::shared_ptr<ov::ITensor>>& tensors,
		                                         const size_t batch_size) {
		   ...
		    return std::make_unique<DiscretePipeline>(config,
		                                                device_handle,
		                                                context,
		                                                graph_ddi_table_ext,
		                                                executorPtr,
		                                                profiling_query.getHandle(),
		                                                command_queues,
		                                                group_ordinal,
		                                                tensors);
		  }
		  ```
		- ![image.png](../assets/image_1714891969895_0.png)
			- ```c++
			      DiscretePipeline(const Config& config,
			                       const ze_device_handle_t& device_handle,
			                       ze_context_handle_t& context,
			                       ze_graph_dditable_ext_curr_t* graph_ddi_table_ext,
			                       const std::shared_ptr<const IExecutor>& executorPtr,
			                       ze_graph_profiling_query_handle_t profiling_handle,
			                       const std::array<std::shared_ptr<CommandQueue>, stage::COUNT>& command_queues,
			                       const uint32_t& group_ordinal,
			                       std::unordered_map<std::string, std::shared_ptr<ov::ITensor>>& tensors){
			        _deviceInputs.allocate(device_handle, context);
			        _deviceOutputs.allocate(device_handle, context);
			        _command_list[stage::UPLOAD].appendMemoryCopy(_deviceInputs.getDevicePtr(desc.first), tensorBuffer, size);
			        _command_list[stage::UPLOAD].appendBarrier();
			        _event[stage::UPLOAD].AppendSignalEvent(_command_list[stage::UPLOAD]);
			        
			        _command_list[stage::READBACK].appendMemoryCopy(tensorBuffer,
			                                                        _deviceOutputs.getDevicePtr(desc.first),
			                                                        size);
			          _event[stage::UPLOAD].AppendWaitOnEvent(_command_list[stage::EXECUTE]);
			  
			          _command_list[stage::EXECUTE].appendGraphExecute(executor->graph(), profiling_handle);
			  
			          _event[stage::UPLOAD].AppendEventReset(_command_list[stage::READBACK]);
			        
			      void push(size_t batch_index) override {	
			          OV_ITT_TASK_CHAIN(ZERO_EXECUTOR_IP_PUSH, itt::domains::LevelZeroBackend, "IntegratedPipeline", "push");
			          if (sync_output_with_fences_) {
			              _command_queue.executeCommandList(*_command_lists.at(batch_index), *_fences.at(batch_index));
			          } else {
			              _command_queue.executeCommandList(*_command_lists.at(batch_index));
			          }
			      };
			  
			      void pull(size_t batch_index) override {
			          OV_ITT_TASK_CHAIN(ZERO_EXECUTOR_IP_PULL, itt::domains::LevelZeroBackend, "IntegratedPipeline", "pull");
			          if (sync_output_with_fences_) {
			              _fences.at(batch_index)->hostSynchronize();
			          } else {
			              _events.at(batch_index)->hostSynchronize();
			          }
			          /// sample npu timestamps if feature was activated
			          if (_npu_profiling != nullptr) {
			              _npu_profiling->sampleNpuTimestamps();
			          }
			      };
			  
			      }
			  ```
		-
		-
- ### [[DirectML]]
	- ![image.png](../assets/image_1714892204288_0.png){:height 608, :width 738}
	- [[metacommand]]