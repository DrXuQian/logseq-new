- Instruction level parallelism
- Problem with ILP all in the same cycle
	- Dependencies bw instructions
	- ![image.png](../assets/image_1713694376662_0.png){:height 347, :width 806}
- Execution stage, can't use forward for such case, need to stall until requirement is met
	- ![image.png](../assets/image_1713694584744_0.png){:height 308, :width 393}
- RAW dependencies
	- ![image.png](../assets/image_1713694879149_0.png){:height 322, :width 397}
- WAW dependencies
	- ![image.png](../assets/image_1713695170003_0.png){:height 363, :width 666}
- Dependency quiz #card
  ![image.png](../assets/image_1713697953542_0.png){:height 473, :width 672}
	- ![image.png](../assets/image_1713697927465_0.png){:height 255, :width 425}
- Removing false dependencies
	- ![image.png](../assets/image_1713698258971_0.png){:height 177, :width 512}
- Duplicate Register Values
	- ![image.png](../assets/image_1713700926572_0.png){:height 333, :width 490}
- Register renaming
	- Architectural registers:
		- registers that programmer/compiler use
	- Physical registers:
		- All places value can go
	- Register allocation table (RAT)
		- Table that says which physical register has value for which architectural register
- RAT Example
	- rename every time we write to a register, read RAT every time we read from a register
	- ![image.png](../assets/image_1713701366540_0.png){:height 398, :width 737}
- RAT quiz #card
  ![image.png](../assets/image_1713701521684_0.png){:height 436, :width 784}
	- ![image.png](../assets/image_1713701663116_0.png){:height 444, :width 889}
- False dependencies after renaming
	- ![image.png](../assets/image_1713701962893_0.png){:height 407, :width 820}
- Instruction level parallelism (ILP)
	- ILP is only property of a program not a processor.
	- ILP is the instructions per cycle assuming perfect processor
	- ![image.png](../assets/image_1713702181673_0.png){:height 378, :width 753}
- ILP example
	- ![image.png](../assets/image_1713702330333_0.png){:height 321, :width 636}
- ILP quiz #card
  ![image.png](../assets/image_1713702382898_0.png){:height 372, :width 677}
	- 1.75
- ILP with control dependencies
	- Structural dependencies (constraint due to hardware resources, i.e. only one adder)
		- No structural dependencies since we are assuming ideal hardware
	- Control dependencies
		- Assume perfect same-cycle branch prediction
		- For the following example, the branches are perfectly predicted since the start of the program
		- ![image.png](../assets/image_1713702909109_0.png){:height 310, :width 506}
- ILP vs IPC
	- ILP is greater or equal to IPC
	- > In the video I ignored the fact that the processor is supposed to be a two-issue processor :( So the first processor (with 1 MUL and 2 ADD units) is being treated as a 3-issue processor in the first cycle.
	- ![image.png](../assets/image_1713703151026_0.png){:height 341, :width 724}
- ILP vs IPC quiz #card
  ![image.png](../assets/image_1713703536041_0.png){:height 383, :width 603}
	- ILP: 3, IPC: 2 (in-order)
- ILP and IPC discussion
	- ![image.png](../assets/image_1713703780356_0.png){:height 400, :width 721}
-