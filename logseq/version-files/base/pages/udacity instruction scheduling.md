- Improving IPC
	- ![image.png](../assets/image_1713703938071_0.png){:height 309, :width 808}
- Tomasulo's algo
	- ![image.png](../assets/image_1713704060426_0.png){:height 406, :width 715}
- The bigger picture
	- Overview:
		- First, there is instruction queue which works like a FIFO, first instructions come in, first out
		- Grasp from the queue and put that into the reservation station, reservation station (might be multiple stations that contains different instructrions for different ALUs) is basically where we store the instructions until the operands become ready
		- Floating point register that stores the register values, when the registers needed for instructions siting in RS is ready. Then the values are fed directly into the reservation station
		- While the instructions are ready to execute, it goes to the ALUs (ADD/MUL). And after the results are generated, the results are broadcasted into a bus. The results will be broadcast into the registers and also broadcast into reservation stations so that the instructions waiting for the values will be executed immediately. (2 inputs, so there are 2 lines connected to the reservation station in the graph from the bus to the RS)
		- If the instruction is load/store from the memory, it will go into the memory unit. Insert the instruction to the load buffer and store buffer where the instruction will queue up for going to memory. When a load comes back from memory, it also goes into the register and RS and also go into store buffer. So the store instruction will load if that is available.
	- ![image.png](../assets/image_1713704429000_0.png){:height 504, :width 862}
- Issue
  collapsed:: true
	- ![image.png](../assets/image_1713705037538_0.png){:height 290, :width 694}
	- example
		- RAT renaming is by rename the register name to the reservation station number
		- The thing is that we are going to overwrite F1 in the RAT for instruction 4 over instruction 2.
		- ![image.png](../assets/image_1713705455031_0.png){:height 418, :width 706}
	- Issue quiz #card
	  ![image.png](../assets/image_1713705563355_0.png){:height 423, :width 742}
		- ![image.png](../assets/image_1713705681922_0.png){:height 242, :width 780}
- Dispatch
  collapsed:: true
	- ![image.png](../assets/image_1713705896201_0.png){:height 231, :width 688}
	- Look up the reservation station and replace the register with the value with the actual value and continue
	- ![image.png](../assets/image_1713705952457_0.png){:height 224, :width 690}
	- continue the execution and continue the process
	- More than 1 instruction ready
		- ![image.png](../assets/image_1713705999375_0.png){:height 257, :width 692}
		- ![image.png](../assets/image_1713706020187_0.png){:height 232, :width 698}
		- Heuristics:
			- **Oldest first** (easy to do )
			- Most dependencies first
			- Random (worst)
	- Dispatch quiz #card
	  ![image.png](../assets/image_1713706443129_0.png){:height 423, :width 852}
		- ![image.png](../assets/image_1713706536915_0.png){:height 228, :width 775}
- Write results (broadcast)
  collapsed:: true
	- ![image.png](../assets/image_1713706661804_0.png){:height 434, :width 682}
	- Steps:
		- Put tag and register on BUS
		- Write to RF (based on RAT)
		- Update RAT (free the entry of F2 to RS1 so that the next user of F2 doesn't wait for RS1, just go to register file for the value)
		- Free RS
		- ![image.png](../assets/image_1713706935786_0.png){:height 443, :width 733}
	- More than 1 broadcast
		- common heuristic
			- the smaller one have higher priority
		- ![image.png](../assets/image_1713707064557_0.png){:height 426, :width 715}
	- Broadcast stale result
		- ![image.png](../assets/image_1713707410899_0.png){:height 392, :width 639}
		- As the upper image shows, if the tag being broadcast doesn't match the RAT, it is perfectly fine.
		- This is because the RAT might be overwritten by a later instruction that writes to the same register. This is fine since the following instructions will just be using the new RAT to locate the new instruction outputs.
- One cycle example
  collapsed:: true
	- ![image.png](../assets/image_1713707576435_0.png){:height 401, :width 800}
	- ![image.png](../assets/image_1713707815472_0.png){:height 119, :width 534}
		- update RAT entry for issue over WR regs. This is because the next instructions need to see RS files instead of updated regs
	- One cycle quiz #card
	  collapsed:: true
	  ![image.png](../assets/image_1713708044841_0.png){:height 483, :width 841}
	  ![image.png](../assets/image_1713708379301_0.png){:height 517, :width 842}
	  ![image.png](../assets/image_1713708662631_0.png){:height 512, :width 842}
		-
		- ![image.png](../assets/image_1713708177106_0.png){:height 482, :width 839}
		- ![image.png](../assets/image_1713708451934_0.png){:height 492, :width 843}
		- ![image.png](../assets/image_1713708753813_0.png){:height 504, :width 843}
- Tomasulo's Algorithm Quiz #card
  collapsed:: true
  ![image.png](../assets/image_1713708923041_0.png){:height 313, :width 878}
	- ![image.png](../assets/image_1713708901604_0.png){:height 318, :width 859}
- Load and store instructions
  collapsed:: true
	- ![image.png](../assets/image_1713709120621_0.png){:height 375, :width 695}
- Long example
	- https://learn.udacity.com/courses/ud007/lessons/8112db18-a27b-450a-9f3d-097a0b5933df/concepts/c14371f8-9e19-415c-a7a3-48599c5db5f9
- Timing quiz #card
  ![image.png](../assets/image_1713770249351_0.png){:height 466, :width 807}
	- ![image.png](../assets/image_1713770342258_0.png){:height 378, :width 452}
	- ![image.png](../assets/image_1713770604980_0.png){:height 366, :width 455}