---
title: Adventures in JIT compilation: Part 2 - an x64 JIT
---
- References:
	- [[Adventures in JIT compilation: Part 1 - an interpreter]]
	- [[How to JIT - an introduction]]
	- [[assembly vs instrinics]]
- Questions:
	- What is encode instructions?
		- Encode machine code to user friendly codes.
	- Differences between asmjit and llvm or libjit?
	- Why use machine code instead of asm declaration?
		- Asm declaration still needs to be translated to machine code by compiler. But with JIT, there should not be any compiler doing this job since the code is directly placed to the corresponding memory location and excited. More importantly, JIT happens during run-time, which makes it not possible for second compilation.
		- So we can use libraries that can present the machine code in a user friendly manner. Then we actually copies the code to the memory, it translates the code to machine code by asmjit library.
	- What exactly does asmjit do? When is the human readable code translated to machine code?
		-
- Reading notes of [Adventures in JIT compilation: Part 2 - an x64 JIT](https://eli.thegreenplace.net/2017/adventures-in-jit-compilation-part-2-an-x64-jit/)
	- Two phases of JIT:
		- Create machine code at program run-time.
		- Execute that machine code, also at program run-time.
	- **Compilers, assemblers and instruction encoding**
		- Two phases of Compilation:
			- The actual source language compiler would translate some higher-level language to target-specific assembly;
			- Then, an assembler would translate assembly to actual machine code
		- Benefits of assembly language over raw machine code:
			- instruction encoding, reader/writer friendly
			- naming labels and procedures for jumps/calls
	- **Simple JIT **
		- use machine code to replace the original C++ code
			- ```c++
			  std::vector<uint8_t> memory(MEMORY_SIZE, 0);
			  
			  // Registers used in the program:
			  //
			  // r13: the data pointer -- contains the address of memory.data()
			  //
			  // rax, rdi, rsi, rdx: used for making system calls, per the ABI.
			  
			  CodeEmitter emitter;
			  
			  // Throughout the translation loop, this stack contains offsets (in the
			  // emitter code vector) of locations for fixup.
			  std::stack<size_t> open_bracket_stack;
			  
			  // movabs <address of memory.data>, %r13
			  emitter.EmitBytes({0x49, 0xBD});
			  emitter.EmitUint64((uint64_t)memory.data());
			  ```
				- use r13 to store the code of BF (memory).
				- CodeEmitter is a simple utility to append bytes and words to a vector of bytes
		- The machine code for different operators:
			- ```c++
			  for (size_t pc = 0; pc < p.instructions.size(); ++pc) {
			    char instruction = p.instructions[pc];
			    switch (instruction) {
			    case '>':
			      // inc %r13
			      emitter.EmitBytes({0x49, 0xFF, 0xC5});
			      break;
			    case '<':
			      // dec %r13
			      emitter.EmitBytes({0x49, 0xFF, 0xCD});
			      break;
			    case '+':
			      // Our memory is byte-addressable, so using addb/subb for modifying it.
			      // addb $1, 0(%r13)
			      emitter.EmitBytes({0x41, 0x80, 0x45, 0x00, 0x01});
			      break;
			    case '-':
			      // subb $1, 0(%r13)
			      emitter.EmitBytes({0x41, 0x80, 0x6D, 0x00, 0x01});
			      break;
			  ```
			- Interpreter for [
				- ```c++
				  case '[':
				    // For the jumps we always emit the instruciton for 32-bit pc-relative
				    // jump, without worrying about potentially short jumps and relaxation.
				  
				    // cmpb $0, 0(%r13)
				    emitter.EmitBytes({0x41, 0x80, 0x7d, 0x00, 0x00});
				  
				    // Save the location in the stack, and emit JZ (with 32-bit relative
				    // offset) with 4 placeholder zeroes that will be fixed up later.
				    open_bracket_stack.push(emitter.size());
				    emitter.EmitBytes({0x0F, 0x84});
				    emitter.EmitUint32(0);
				    break;
				  ```
					- open_bracket_stack stores the position of the [
			- Interpreter for ]
				- ```c++
				  case ']': {
				    if (open_bracket_stack.empty()) {
				      DIE << "unmatched closing ']' at pc=" << pc;
				    }
				    size_t open_bracket_offset = open_bracket_stack.top();
				    open_bracket_stack.pop();
				  
				    // cmpb $0, 0(%r13)
				    emitter.EmitBytes({0x41, 0x80, 0x7d, 0x00, 0x00});
				  
				    // open_bracket_offset points to the JZ that jumps to this closing
				    // bracket. We'll need to fix up the offset for that JZ, as well as emit a
				    // JNZ with a correct offset back. Note that both [ and ] jump to the
				    // instruction *after* the matching bracket if their condition is
				    // fulfilled.
				  
				    // Compute the offset for this jump. The jump start is computed from after
				    // the jump instruction, and the target is the instruction after the one
				    // saved on the stack.
				    size_t jump_back_from = emitter.size() + 6;
				    size_t jump_back_to = open_bracket_offset + 6;
				    uint32_t pcrel_offset_back =
				        compute_relative_32bit_offset(jump_back_from, jump_back_to);
				  
				    // jnz <open_bracket_location>
				    emitter.EmitBytes({0x0F, 0x85});
				    emitter.EmitUint32(pcrel_offset_back);
				  
				    // Also fix up the forward jump at the matching [. Note that here we don't
				    // need to add the size of this jmp to the "jump to" offset, since the jmp
				    // was already emitted and the emitter size was bumped forward.
				    size_t jump_forward_from = open_bracket_offset + 6;
				    size_t jump_forward_to = emitter.size();
				    uint32_t pcrel_offset_forward =
				        compute_relative_32bit_offset(jump_forward_from, jump_forward_to);
				    emitter.ReplaceUint32AtOffset(open_bracket_offset + 2,
				                           pcrel_offset_forward);
				    break;
				  }
				  ```
					- ] first pop the top of open_bracket_stack, which is the corresponding position of the matching [ of current ]
					- Then jnz to the [ location using 32-bit offset computed as line 20-22
			- Invoke the JITed machine code stored in vector memory.data()
				- ```c++
				  // ... after the compilation loop
				  
				  // The emitted code will be called as a function from C++; therefore it has to
				  // use the proper calling convention. Emit a 'ret' for orderly return to the
				  // caller.
				  emitter.EmitByte(0xC3);
				  
				  // Load the emitted code to executable memory and run it.
				  std::vector<uint8_t> emitted_code = emitter.code();
				  JitProgram jit_program(emitted_code);
				  
				  // JittedFunc is the C++ type for the JIT function emitted here. The emitted
				  // function is callable from C++ and follows the x64 System V ABI.
				  using JittedFunc = void (*)(void);
				  
				  JittedFunc func = (JittedFunc)jit_program.program_memory();
				  func();
				  ```
					- See [[How to JIT - an introduction]] for details on how to run JIT program from memory
			- **Taking JIT for a spin **
				- `objdump -D -b binary -mi386:x86-64` to dump the assembly code
				- Why JIT is much faster than naïve interpreter
					- What is needed to interpret a single instruction:
						- Advance pc and compare it to program size.
						- Grab the instruction at pc.
						- Switch on the value of the instruction to the right case.
						- Execute the case.
					- This requires a whole sequence of machine instructions. While JIT just emits a single instruction without any branches.
	- **Use asmjit to encode instructions**
		- library for user friendly machine code generation.
		- DONE read the source code of eli's repo