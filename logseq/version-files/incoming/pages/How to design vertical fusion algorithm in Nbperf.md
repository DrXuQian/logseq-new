- Update in 2023.10.15
	- Issue with DFS order based recursive search is that the order matters a lot
	- Loop all shave kernels, and search for possible optimization opportunities.
	- For a given shave kernel:
		- Loop all possible tiling options
		- Probe surrounding DPU kernels using BFS order.
- Update in 2023.10.12
	- Recursive search
		- For a given graph with $$n$$ nodes, we should have $$cost(i, j)$$ where $$i$$ is the head and $$j$$ is the tail of the subgraph.
		- And for each subgraph, we should have the tiling for that subgraph and the cost for that subgraph. We should have $$n^2$$ costs. So that cost is relatively huge.
		- Brings 2 questions:
			- Cost estimation, the cost should be estimated well. There is compute eff and halo region consideration also.
			- Tiling number search, essentially, minimum tiling number for a layer that doesn't involve halo region should stay the same. But when there is halo region, we need to consider that memory when calculation tiling number. So there is this issue.
		- If we already have the cost for every $$cost(i, j)$$, the optimal solution is easy to determine.
		- Prune the search:
			- If a node can't vertical fuse (can't fit in CMX with the subgraph tiling axis/not supported), we should split that to a single subgraph. And the nodes before and after should be separate subgraphs.
			- If a subgraph exceeds the maximum complexity we can handle, more than 50 nodes or so. Split that in half. Should find the node with minimum spill cost.
- Video of Fathom:
  collapsed:: true
	- [vertical fusion in Fathom](https://intel.sharepoint.com/sites/VPUIPArchitecture/Shared%20Documents/Forms/AllItems.aspx?id=%2Fsites%2FVPUIPArchitecture%2FShared%20Documents%2FNCE%20Software%20Architecture%2FNNARCH%20%2D%20TechTalks%20%26%20Trainings%2FTech%20Talk%20%2D%20Vertical%20Fusion%2Emp4&parent=%2Fsites%2FVPUIPArchitecture%2FShared%20Documents%2FNCE%20Software%20Architecture%2FNNARCH%20%2D%20TechTalks%20%26%20Trainings&OR=Teams%2DHL&CT=1674982616133&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiIyNy8yMzAxMDEwMDkxMyIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)
		- ![image.png](../assets/image_1674998276681_0.png){:height 464, :width 793}
		- ![image.png](../assets/image_1674998331025_0.png){:height 459, :width 795}
		- ![image.png](../assets/image_1674998424470_0.png){:height 408, :width 791}
		- ![image.png](../assets/image_1674998498472_0.png){:height 427, :width 795}
		- ![image.png](../assets/image_1674998561715_0.png){:height 445, :width 795}
		- ![image.png](../assets/image_1674998661404_0.png){:height 447, :width 789}
		- Shallow vertical fusion and deep vertical fusion:
			- Trade off bw compute efficiency and DMA time
		- ![image.png](../assets/image_1674999195573_0.png){:height 463, :width 788}
			- Use circular buffer to avoid reloading of some of the rows.
	-
- Decompose the questions:
  collapsed:: true
	- How to determine whether a layer should be part of a vertical fusion graph or not?
		- Fathom:
			- no vertical fusion for the input and output layer.
			- Don't see vertical fusion used to calculate cost of VPUNN cycles or memory in:
				- [Strategy Manager in Fathom](https://github.com/intel-innersource/frameworks.ai.vpu.presilicon.fathom/blob/8fc6ca50e3cafe6ad4a5c80a2adf20e2de5c6593/src/Controllers/StrategySelection/StrategyManager.py#L552)
				- Ultimately the `self.cost` would call VPUNN for the cycles, and vertical fusion is not used anywhere there
				- For the memory calculation and DMA time, vertical fusion is also not used.
		- Nbperf:
			- plan1
				- only spatial non-mixing layer can be considered as vertical fusion
					- s==1 convolution pooling
					- activation functions
					- with only one input and one output
					- layers that will trigger temporal tiling
			- plan2
				- + convolution with k > 1
	- Should we spill the first layer and last layer of vertical fusion subgraph?
		- Fathom:
			- ```python
			  def get_subgraph_spilling(graph, subgraph):
			      # Check if the subgraph input tensor is equivalent to the
			      input_layer = graph.layer_by_type(parser.Input)
			      output_layer = graph.layer_by_type(parser.Output)
			  
			      assert len(input_layer) == 1  # Single input network
			      assert len(output_layer) == 1  # Single output network
			  
			      input_ddr = input_layer[0].getOutputTensors()[0] == subgraph.nodes[subgraph.source[0]]["ref"].getInputTensors()[0]
			      output_ddr = output_layer[0].getInputTensors()[0] == subgraph.nodes[subgraph.sink[0]]["ref"].getOutputTensors()[0]
			  
			      return input_ddr, output_ddr
			  ```
		- Nbperf:
			- always spill the first and last layer of the vertical fusion subgraph
	- How to determine the temporal tiling strategy of the VF subgraph?
		- Fathom:
			- ==Maximum number of split on the H dims==
			- This can be dangerous
			- ```python
			      # Get the number of splits (TODO: less naive strategy)
			      vertical_splits = max([strategy.get_streaming(layer)["H"] for layer in graph.layers])
			  ```
		- Nbperf:
			- extract the subgraph and set all layers within the subgraph TOH and search for the tiling number of the layer with most memory footprint
			- set all layers with the same tiling number
	- How to make sure there is no memory issue with the vertical fusion subgraph? (since there is no spill in bw the subgraph)
		- Fathom:
			- there is an assertion for this:
				- ```python
				          # Get the tensor shapes and offsets for every output split
				          peak_footprint, shapes = getSplitFootprint(graph.layers, strategy, shape, output_offset=offset)
				          if peak_footprint > strategy.cluster_memory * 100:
				              error(RuntimeError, "Split exceed cluster size")
				  ```
		- Nbperf:
			- should be no memory issue, assert when find issue
- [[Vertical fusion in Fathom]]
  collapsed:: true
	- Pass in Fathom:
		- src/Controllers/OptimizationPasses/IndividualPasses/VerticalFusion.py
	- Description:
		- >  Vertical fusion compiler pass.
		      Decompose a network into parallel branches in order to
		      minimize activation spilling.
	- Code of the main function:
		- ```python
		  def vertical_fusion(g, arguments):
		      '''
		      Vertical fusion compiler pass.
		      Decompose a network into parallel branches in order to
		      minimize activation spilling.
		      '''
		  
		      # Skip if vertical fusion is disabled
		      if not arguments.vertical_fusion:
		          return g
		  
		      strategy = arguments.strategy_selection_manager
		      input_segmentation = arguments.device.get_input_segmentation()
		  
		      # Lis of layers to add/remove from the original network
		      new_layers, remove_layers = [], []
		      # Get all vertical fusion subgraphs
		      for subgraph in vertical_fusion_subgraphs(g, strategy):
		          input_ddr, output_ddr = get_subgraph_spilling(g, subgraph)
		          new_layers += topological_vertical_fusion(subgraph, strategy, input_ddr, output_ddr, input_segmentation)
		          remove_layers += subgraph.layers
		  
		      # Return the transformed network
		      return transform_network(g, new_layers, remove_layers)
		  ```
- Notes from discussion with Victor:
  collapsed:: true
	- greedy algo
		- find groups of layers
		- spatial feature map not change
		- control max depth (reception field, extra compute cost)
		- no branch in subgraph
		- spill happens in subgraph
		- each operator need to met conditions
			- etlwise op
			- dpu op, stride==1
			- activation func
		- TOH and split_num need to be aligned.
			- check the peak memory consumption
			- use the peak memory to decide the split-num
	- position of pass
		- no dependency with spatial tiling
		- happen before temporal tiling
-