- Problem:
	- Insert copy of weights to graph break the dfs order of the network
	  collapsed:: true
		- In Fathom:
			- ```python
			      def graph_sorting(sg):
			          if get_all_topological_sorts:
			              return nx.all_topological_sorts(sg)
			          else:
			              return [list(nx.topological_sort(sg)), list(nx.lexicographical_topological_sort(sg))]
			  ```
		- After inserting copy to the graph, we would have many copy nodes as network inputs, how to address the scheduling order issue.
			- ```python
			  >>> import networkx as nx
			  >>> DG = nx.DiGraph([(1, 2), (2, 3), (3, 4), (5, 2), (6, 3), (7, 4)])
			  >>> list(nx.lexicographical_topological_sort(DG))
			  [1, 5, 2, 6, 3, 7, 4]
			  >>> list(nx.topological_sort(DG))
			  [7, 6, 5, 1, 2, 3, 4]
			  >>> list(nx.all_topological_sorts(DG))
			  [[7, 6, 5, 1, 2, 3, 4], [7, 6, 1, 5, 2, 3, 4], [7, 5, 1, 2, 6, 3, 4], [7, 5, 1, 6, 2, 3, 4], [7, 5, 6, 1, 2, 3, 4], [7, 1, 6, 5, 2, 3, 4], [7, 1, 5, 2, 6, 3, 4], [7, 1, 5, 6, 2, 3, 4], [6, 5, 1, 2, 3, 7, 4], [6, 5, 1, 2, 7, 3, 4], [6, 5, 1, 7, 2, 3, 4], [6, 5, 7, 1, 2, 3, 4], [6, 1, 7, 5, 2, 3, 4], [6, 1, 5, 2, 3, 7, 4], [6, 1, 5, 2, 7, 3, 4], [6, 1, 5, 7, 2, 3, 4], [6, 7, 5, 1, 2, 3, 4], [6, 7, 1, 5, 2, 3, 4], [5, 1, 2, 7, 6, 3, 4], [5, 1, 2, 6, 3, 7, 4], [5, 1, 2, 6, 7, 3, 4], [5, 1, 7, 6, 2, 3, 4], [5, 1, 7, 2, 6, 3, 4], [5, 1, 6, 2, 3, 7, 4], [5, 1, 6, 2, 7, 3, 4], [5, 1, 6, 7, 2, 3, 4], [5, 7, 6, 1, 2, 3, 4], [5, 7, 1, 2, 6, 3, 4], [5, 7, 1, 6, 2, 3, 4], [5, 6, 1, 2, 3, 7, 4], [5, 6, 1, 2, 7, 3, 4], [5, 6, 1, 7, 2, 3, 4], [5, 6, 7, 1, 2, 3, 4], [1, 7, 6, 5, 2, 3, 4], [1, 7, 5, 2, 6, 3, 4], [1, 7, 5, 6, 2, 3, 4], [1, 6, 5, 2, 3, 7, 4], [1, 6, 5, 2, 7, 3, 4], [1, 6, 5, 7, 2, 3, 4], [1, 6, 7, 5, 2, 3, 4], [1, 5, 2, 7, 6, 3, 4], [1, 5, 2, 6, 3, 7, 4], [1, 5, 2, 6, 7, 3, 4], [1, 5, 7, 6, 2, 3, 4], [1, 5, 7, 2, 6, 3, 4], [1, 5, 6, 2, 3, 7, 4], [1, 5, 6, 2, 7, 3, 4], [1, 5, 6, 7, 2, 3, 4]]
			  ```
- Copy operator:
	- {{embed ((63f74a15-d18a-4b85-87f1-4f73b8f34508))}}
- CMX Memory allocation:
	- Insert copy when necessary. If there is operator on DDR, need to insert Copy before and after.
	-
- Generate memory instructions from graph
	- Every tensor should associate with Distributed Buffer
		- That is built with a `dict`, this can elaborate to a `user defined dict`.
- Operators that are going to be mapped to DMA:
	- Slice; Concat; Depth2Space; CopyOp;
	- every DMA operator should define DMADirection..
- [[Multi-cluster strategy in Nbperf]]
-