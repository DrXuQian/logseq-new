- Context:
	- [Yesterday 11:45 AM] Crews, Darren S
	  there is an effort proposed to take more of the SW/compiler stack into MLIR - like OpenVINO.  It would work across IPs and have an end to end MLIR compiler like from framework input to codegen output, do either of you have interest in participating in a regular forum on this topic?
	- From Alex email:
		- Darren mentioned that the VPU compiler is written in MLIR and mainly leverages affine, but it gets subgraphs it optimizes. I know from my previous life with working with the PlaidML team, that we hooked up OV IR with PlaidML’s Tile and we lowered Tile to LinAlg and then ukernels on CPU.
		- What is the current high level Ops descriptions / dialects you guys are using in MLIR? Or if all of the first questions don’t make sense, where is the current entry point into MLIR (as the VPU compiler seems to be based on MLIR)? My question is now, how hard/simple is it to transform a graph from OV into MLIR StableHLO or better MLIR LinAlg?
		- Sorry, some of these questions might trivial or make straightout no sense, however, I’m still learning, and I would like to understand where differences between stacks are.
- [[MLIR]]