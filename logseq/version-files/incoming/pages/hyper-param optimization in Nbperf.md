- [[hyper-param optimization in Fathom]]
- How to extract pivot graph and subgraphs in Fathom?
	- ![image.png](../assets/image_1676249873191_0.png)
	- ```python
	  def extractSubgraphs(g):
	      # Extract subgraphs from a network
	      # Return a list of linear subgraphs and the relative pivot network
	      # For linear subgraph definition, see the appropriate function above
	      # A pivot network is a reduction of the original one with only the edges
	      # with less than 2 neighbours
	  ```
- We can follow the fathom method to do this, recursive shortest path algorithm.
- Actually, it would be quite easy to cooperate vertical fusion in the optimization process
	- Think of the vertical fusion subgraph as a whole. And the cost can be calculated even with the halo region effect.
	- The cost of `vf_subgraph_cost(input_node, input_cfg, output_node, output_cfg)` is determined by the input node and cfg and the output node and cfg.
	- For each input node, cfg and output node, cfg pair, we would have many options in the middle, but we can call the shortest path recursively according to fathom. The main difference is that we have multiple options of inserting vertical fusion terminal points in the middle. I think the best way is by bisect search through the process.