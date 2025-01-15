# HC34-T2: Heterogeneous Compilation in MLIR
- {{video https://www.youtube.com/watch?v=VFexAjUoTZI&ab_channel=hotchipsvideos}}
- MLIR tutorial by Hot Chips:
	- First Presenter from Google:
	  collapsed:: true
		- MLIR is a collection of modular and reusable software components that enables the progressive lowering of high level operations.
		- Originally we have many graph compilers, code in common but re-implemented
		- MLIR allow multiple levels of IR at the same time:
			- ![image.png](../assets/image_1694392764315_0.png){:height 205, :width 306}
		- Always easier to preserve than recover info
			- ![image.png](../assets/image_1694392841253_0.png){:height 181, :width 582}
			- Example:
				- XLA need to recover the dataflow computing
				- ![image.png](../assets/image_1694392878189_0.png)
		- Mix and Match in a single IR
			- ![image.png](../assets/image_1694393003123_0.png){:height 232, :width 708}
		- Reuse MLIR dialect for common purposes without reinventing the wheel
			- ![image.png](../assets/image_1694393108442_0.png){:height 351, :width 567}
		- Premature lowering is the predecessor of all evil.
		- How is MLIR different?
			- Choose the level of representation that is right for your device.
			- ![image.png](../assets/image_1694393434586_0.png){:height 356, :width 703}
		- MLIR: reusable compiler abstraction toolbox:
			- ![image.png](../assets/image_1694393561713_0.png){:height 354, :width 702}
		- What is in the box?
			- Operations:
				- ![image.png](../assets/image_1694393692070_0.png){:height 306, :width 755}
			- MLIR syntax:
				- ![image.png](../assets/image_1694393793275_0.png){:height 284, :width 770}
			- Dialects:
				- ![image.png](../assets/image_1694393902910_0.png){:height 415, :width 746}
			- Defining a Dialect:
				- Ops can be defined:
					- ![image.png](../assets/image_1694394027069_0.png){:height 195, :width 320}
				- What is ODS?
					- ![image.png](../assets/image_1694394045568_0.png){:height 378, :width 393}
			- Progressive disclosure
				- ![image.png](../assets/image_1694394184207_0.png){:height 367, :width 705}
			-
	- Second Presenter from Nod.AI
		- Many optimization opportunities are missed if start at LLVM IR level
		- Current dialect ecosystem in MLIR:
			- ![image.png](../assets/image_1694337578685_0.png){:height 686, :width 915}
		- Linalg Dialect #linalgdialect
			- Linalg defines a small set of core named op that the front-end dialect can lower to
			- Other ops are generic op:
				- ![image.png](../assets/image_1694394865264_0.png){:height 456, :width 819}
		- Vector Dialect
		- GPU Dialect
			- abstractions for retargetable GPU models
		- Accelerator-Specific Dialects
			- ![image.png](../assets/image_1694395079621_0.png){:height 411, :width 695}
		- Perceptron code generation
			- ![image.png](../assets/image_1694395187809_0.png){:height 462, :width 890}
			- ![image.png](../assets/image_1694395266283_0.png){:height 452, :width 892}
			- Overview of CPU code gen:
			  collapsed:: true
				- ![image.png](../assets/image_1694395391146_0.png){:height 382, :width 873}
				- Dispatch region formation:
				  collapsed:: true
					- ![image.png](../assets/image_1694395416676_0.png){:height 441, :width 838}
				- Tile and distribute to workgroups
				  collapsed:: true
					- ![image.png](../assets/image_1694395514533_0.png){:height 460, :width 843}
					- ![image.png](../assets/image_1694395545240_0.png){:height 453, :width 845}
					- ![image.png](../assets/image_1694395598550_0.png){:height 468, :width 845}
				- Vectorize
				  collapsed:: true
					- ![image.png](../assets/image_1694395636943_0.png){:height 459, :width 846}
				- Bufferize
				  collapsed:: true
					- ![image.png](../assets/image_1694395686189_0.png){:height 465, :width 854}
				- Lower to hardware
				  collapsed:: true
					- ![image.png](../assets/image_1694395706847_0.png){:height 479, :width 885}
			- Overview of GPU code gen:
				- ![image.png](../assets/image_1694395744102_0.png){:height 315, :width 893}
			-
	- Third Presenter from ARM
		- MLIR in ML Frameworks:
		  collapsed:: true
			- Iree
			- Modular
			- ![image.png](../assets/image_1694351021077_0.png){:height 277, :width 461}
		- Connecting frameworks and code gen
		  collapsed:: true
			- ![image.png](../assets/image_1694404144807_0.png)
		- Mid-Level MLIR dialects:
		  collapsed:: true
			- ![image.png](../assets/image_1694404189376_0.png){:height 360, :width 582}
		- TOSA
		  collapsed:: true
			- ![image.png](../assets/image_1694404246375_0.png){:height 306, :width 732}
			- ![image.png](../assets/image_1694404309257_0.png){:height 361, :width 474}
			- Example: Quantized Conv2D
			  collapsed:: true
				- ![image.png](../assets/image_1694404397049_0.png){:height 338, :width 775}
				- After:
				- ![image.png](../assets/image_1694404427230_0.png)
			- Example: Complex Matmul
			  collapsed:: true
				- ![image.png](../assets/image_1694404588200_0.png){:height 366, :width 835}
				- ![image.png](../assets/image_1694404600090_0.png){:height 447, :width 835}
				-
		- TOSA to code gen:
			- ![image.png](../assets/image_1694404685430_0.png){:height 372, :width 744}
	- Four Presenter from Microsoft about circt
		- MLIR based compiler stack for HW design and verification
- # MLIR Open Meeting 2021-10-7: The Torch MLIR Project
- {{video https://www.youtube.com/watch?v=QbNkex-gizs&ab_channel=MLIR}}
	- From Nod AI:
	  collapsed:: true
		- What is torch MLIR?
			- ![image.png](../assets/image_1694698656415_0.png)
		- One point this guy mentioned for going to MLIR is that the dialect transformation would be super easy than converting from other non-MLIR graph representations. Other things are the good features in MLIR, including pattern rewrite and so on. This is kind of like std in C++, MLIR provides a bunch of effective approach for user to pick from.
		- ![image.png](../assets/image_1694699787442_0.png)
			- Downstream focus on the unique value instead of building pytorch/MLIR frontend.
		- ![image.png](../assets/image_1694699886444_0.png){:height 450, :width 852}
			- No need to invest that much resources on keeping up with the framework
	- From Google Engineer:
		- ![image.png](../assets/image_1694700503023_0.png){:height 444, :width 844}
		- Some technical details that make me sleepy...zzz
		- Frontends and backends:
			- ![image.png](../assets/image_1694700953511_0.png)
		- Frontends:
			- ![image.png](../assets/image_1694701097259_0.png){:height 299, :width 885}
		- TorchScript:
			- ![image.png](../assets/image_1694701156974_0.png){:height 376, :width 765}
				- good for inference and not good for training
			- ![image.png](../assets/image_1694701251010_0.png){:height 521, :width 965}
			- ![image.png](../assets/image_1694701413276_0.png){:height 572, :width 966}
		- TorchFX:
		- LazyTensorCore:
			-
-