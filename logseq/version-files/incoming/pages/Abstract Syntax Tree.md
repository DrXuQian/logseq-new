alias:: ast

- >  In computer science, an abstract syntax tree (AST), or just syntax tree, is a tree representation of the abstract syntactic structure of text (often source code) written in a formal language. ^^Each node of the tree denotes a construct occurring in the text.^^
  The syntax is "abstract" in the sense that it does not represent every detail appearing in the real syntax, but rather just the structural or content-related details. ^^For instance, grouping parentheses are implicit in the tree structure, so these do not have to be represented as separate nodes. Likewise, a syntactic construct like an if-condition-then statement may be denoted by means of a single node with three branches.^^
- The abstract syntax tree for
	- ```C++
	  while b ≠ 0:
	      if a > b:
	          a := a - b
	      else:
	          b := b - a
	  return a
	  ```
	- ![Abstract_syntax_tree_for_Euclidean_algorithm.svg](../assets/Abstract_syntax_tree_for_Euclidean_algorithm_1646624115018_0.svg)