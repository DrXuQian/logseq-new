---
title: indirect mapping
---

- Reference:
	 - [[cache and register]]

	 - [[专栏：GEMM optimization]]

- Reading notes of https://www.cs.helsinki.fi/u/kerola/tito/koksi_doc/memaddr.html
	 - Immediate Operand
		 - Ex:   `LOAD R1, =100`	
			 - Load the number 100 to register R1.

	 - Direct Addressing
		 - For example:
			 - 1) `LOAD R1, 100`	  
				 - Load the content of memory address 100 to register R1.

			 - 2) `LOAD R1, R2`	  
				 - Load the content of register R2 to register R1.

	 - Indirect Addressing
		 - 1) `LOAD R1, @100`
			 - Load the content of memory address stored at memory address 100 to the register R1.

		 - 2) `LOAD R1, @R2`
			 - Load the content of the memory address stored at register R2 to register R1

- Example:
	 - https://github.com/flame/how-to-optimize-gemm/wiki/Optimization_1x4_9

	 - ![](../assets/YO5m5vDxc3.png)
		 - There is a special machine instruction to then access the element at `bp0_pntr+1` that does not require the pointer to be updated.

		 - Compiler automatically did this
