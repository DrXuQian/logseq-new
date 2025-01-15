- Have a list of execution providers in order. Try assign the highest priority one as much as possible.
- Flow of onnx runtime:
	- ![ONNX Runtime high level system architecture](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/228d22d3-6e3e-48b1-811c-1d48353f031c.png)
	- Starting from an ONNX model, ONNX Runtime first converts the model graph into its in-memory graph representation.
	- It performs a set of provider independent [optimizations](https://onnxruntime.ai/docs/performance/graph-optimizations).
	- It partitions the graph into a set of subgraphs based on the available execution providers.
	- Each subgraph is assigned to an execution provider. We ensure that a subgraph can be executed by an execution provider by querying the capability of the execution provider using the `GetCapability()` API.
- From the code:
	- https://github.com/microsoft/onnxruntime/blob/2acdc3cd8290c2395c95fcc5f33e719c2abb0fe3/onnxruntime/core/framework/graph_partitioner.cc#L237
	- ```c++
	  Status GraphPartitioner::Partition(Graph& graph, bool export_dll, FuncManager& func_mgr) const { 
	  // It is a greedy partitioning algorithm per provider preferences user provided when calling ONNX RUNTIME right now.
	    // 1. Execution providers' capabilities are checked one by one.
	    // 2. All sub-graphs that an execution provider returns will be assigned to it if it's not assigned yet.
	    //    NOTE: A 'sub-graph' is a subset of nodes within the current Graph instance.
	    //          The control flow nodes have nested Graph instance/s which are also called subgraphs,
	    //          but are completely separate Graph instances and not a subset of nodes within a single Graph instance.
	    // 3. CPU execution provider is expected to be able to run any node and is the last one in execution provider
	    //    preference.
	  ```
-