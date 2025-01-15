- #fathom
- https://github.com/intel-innersource/frameworks.ai.vpu.presilicon.fathom/blob/main/src/Controllers/StrategySelection/StrategyManager.py#L73
- Strategy Manager
	- Dijkstra
		- Cost for the algorithm: Split cost
			- https://github.com/intel-innersource/frameworks.ai.vpu.presilicon.fathom/blob/b0f8cee63375c55fa450e02b7ca770737f3bddf3/src/Controllers/StrategySelection/StrategyManager.py#L535
			- Query for a layer:
				- `isi_strategy` is the split strategy of a certain layer
				- ```
				  ISIStrategy.Clustering, ISIStrategy.SplitOverH, ISIStrategy.SplitOverW,
				  ISIStrategy.SplitOverHW, ISIStrategy.SplitOverKH,
				  ISIStrategy.SplitOverHOverlapped, ISIStrategy.HKSwitch
				  ```
				- ```python
				             # Builds each layer's description for later comparison with others
				        		query.append({
				                  "name": name,
				                  "layer": this_node.className(),
				                  "clusters": query_number("clusters"),
				                  "isi_strategy": param_dict["isi_strategy"].lower(),
				                  "sparsity": query_string("sparsity"),
				                  "weight_sparsity": query_string("weight_sparsity"),
				                  "dtype": query_string("dtype"),
				                  "spilled": query_string("spilled"),
				                  "swizzling": query_string("swizzling"),
				                  "vertical_fusion": query_string("vertical_fusion"),
				                  "fractional_batch": query_number("fractional_batch"),
				                  "inplace": query_string("inplace"),
				                  "nops": query_string("nops"),
				                  "streaming": {
				                      key.lower(): dict(param_dict["streaming"])[key] for key in dict(param_dict["streaming"])
				                  },
				                  "padding": {
				                      "left": node_padding[1][0],
				                      "right": node_padding[1][1],
				                      "top": node_padding[0][0],
				                      "bottom": node_padding[0][1]
				                  },
				                  "kernel": {
				                      "h": node_kernel[0],
				                      "w": node_kernel[1],
				                  },
				                  "shape": {
				                      "c": node_input_shape[1],
				  
				                      "b": node_output_shape[0],
				                      "k": node_output_shape[1],
				                      "h": node_output_shape[2],
				                      "w": node_output_shape[3]
				                  }
				              })
				  ```
		- Interesting observation in SE-Size
			- ```
			              orig_tensor_size = self.tensor_size(tensor, splits, strategy, nClusters, streaming, target_dtype=target_dtype, batch=batch)
			              se_size = np.dtype(np.int32).itemsize * np.product([(a + b - 1) // b for a, b in zip(tensor.shape[2:], splits[2:])])
			              se_size *= streaming["K"]
			              se_size *= nClusters if strategy in [ISIStrategy.SplitOverK, ISIStrategy.SplitOverKH, ISIStrategy.HKSwitch] else 1
			              sm_size = orig_tensor_size // (8 * np.ceil(target_dtype.itemsize).astype(int))
			              return batch * (orig_tensor_size + se_size + sm_size)
			  ```