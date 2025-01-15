---
title: Adventures in JIT compilation: Part 1 - an interpreter
---

- References
	- [[MKLDNN microkernel]]
	- [[Dynamic Shape in nnCompiler]]
	- [[Tracing just-in-time compilation]]
	- [[How to JIT - an introduction]]
	- [[Adventures in JIT compilation: Part 2 - an x64 JIT]]
- JIT compiler compile the code on the fly, which means during the RUNTIME. 
  id:: 515a67fe-0f21-403d-8c70-b1c6612a7c3d
	- Benefits:
		- JIT have access to the RUNTIME information, so it can compile with better optimization strategy.
		- https://en.wikipedia.org/wiki/Just-in-time_compilation
			- > Most often, this consists of [source code](https://en.wikipedia.org/wiki/Source_code) or more commonly [bytecode](https://en.wikipedia.org/wiki/Bytecode) translation to [machine code](https://en.wikipedia.org/wiki/Machine_code), which is then executed directly. A system implementing a JIT compiler typically continuously analyses the code being executed and identifies parts of the code where the speedup gained from compilation or recompilation would outweigh the overhead of compiling that code.
- Reading notes of [Adventures in JIT compilation: Part 1 - an interpreter](https://eli.thegreenplace.net/2017/adventures-in-jit-compilation-part-1-an-interpreter/)
	- BrainFuck language:
		- Parser
			- very simple and straightforward, nothing but the 8 characters should be treat as valid code
			- ```c++
			  struct Program {
			    std::string instructions;
			  };
			  
			  Program parse_from_stream(std::istream& stream) {
			    Program program;
			  
			    for (std::string line; std::getline(stream, line);) {
			      for (auto c : line) {
			  if (c == '>' || c == '<' || c == '+' || c == '-' || c == '.' ||
			      c == ',' || c == '[' || c == ']') {
			    program.instructions.push_back(c);
			  }
			      }
			    }
			    return program;
			  }
			  ```
		- Actual interpreter
			- ```c++
			  constexpr int MEMORY_SIZE = 30000;
			  
			  void simpleinterp(const Program& p, bool verbose) {
			    // Initialize state.
			    std::vector<uint8_t> memory(MEMORY_SIZE, 0);
			    size_t pc = 0;
			    size_t dataptr = 0;
			  
			    while (pc < p.instructions.size()) {
			      char instruction = p.instructions[pc];
			      switch (instruction) {
			      case '>':
			  dataptr++;
			  break;
			      case '<':
			  dataptr--;
			  break;
			      case '+':
			  memory[dataptr]++;
			  break;
			      case '-':
			  memory[dataptr]--;
			  break;
			      case '.':
			  std::cout.put(memory[dataptr]);
			  break;
			      case ',':
			  memory[dataptr] = std::cin.get();
			  break;
			  
			      // [...]
			  ```
			- Rather trivial but the [] is rather interesting
			- control flow ops: []
				- [ - jump forward if the current data location is zero
					- ```c++
					  case '[':
					    if (memory[dataptr] == 0) {
					      int bracket_nesting = 1;
					      size_t saved_pc = pc;
					  
					      while (bracket_nesting && ++pc < p.instructions.size()) {
					        if (p.instructions[pc] == ']') {
					  bracket_nesting--;
					        } else if (p.instructions[pc] == '[') {
					  bracket_nesting++;
					        }
					      }
					  
					      if (!bracket_nesting) {
					        break;
					      } else {
					        DIE << "unmatched '[' at pc=" << saved_pc;
					      }
					    }
					    break;
					  ```
					- [ and ] can be nested, so we need to find the corresponding ] to figure out where to jump.
				- ] - jump to earlier [ if the current data location is not zero
					- ```c++
					  case ']':
					    if (memory[dataptr] != 0) {
					      int bracket_nesting = 1;
					      size_t saved_pc = pc;
					  
					      while (bracket_nesting && pc > 0) {
					        pc--;
					        if (p.instructions[pc] == '[') {
					  bracket_nesting--;
					        } else if (p.instructions[pc] == ']') {
					  bracket_nesting++;
					        }
					      }
					  
					      if (!bracket_nesting) {
					        break;
					      } else {
					        DIE << "unmatched ']' at pc=" << saved_pc;
					      }
					    }
					    break;
					  
					  ```
					- [full code](https://github.com/eliben/code-for-blog/blob/master/2017/bfjit/simpleinterp.cpp)
		- Measuring the performance of BF programs
			- Optimized interpreter - take 1
				- Not necessary to look for the matching brackets every time. The position is fixed.
				- ```c++
				  std::vector<size_t> compute_jumptable(const Program& p) {
				    size_t pc = 0;
				    size_t program_size = p.instructions.size();
				    std::vector<size_t> jumptable(program_size, 0);
				  
				    while (pc < program_size) {
				      char instruction = p.instructions[pc];
				      if (instruction == '[') {
				        int bracket_nesting = 1;
				        size_t seek = pc;
				  
				        while (bracket_nesting && ++seek < program_size) {
				   if (p.instructions[seek] == ']') {
				     bracket_nesting--;
				   } else if (p.instructions[seek] == '[') {
				     bracket_nesting++;
				   }
				        }
				  
				        if (!bracket_nesting) {
				   jumptable[pc] = seek;
				   jumptable[seek] = pc;
				        } else {
				   DIE << "unmatched '[' at pc=" << pc;
				        }
				      }
				      pc++;
				    }
				  
				    return jumptable;
				  }
				  ```
				- jumptable[i] holds the offset for matching brackets.
				- ```c++
				  case '[':
				    if (memory[dataptr] == 0) {
				      pc = jumptable[pc];
				    }
				    break;
				  case ']':
				    if (memory[dataptr] != 0) {
				      pc = jumptable[pc];
				    }
				    break;
				  ```
				- simple for brackets in main loop
			- Optimized interpreter - take 2
				- Many ops repeated many times, we need extra information for every BF instruction, so we'll just translate the Program into a sequences of ops of the type:
					- ```c++
					  enum class BfOpKind {
					    INVALID_OP = 0,
					    INC_PTR,
					    DEC_PTR,
					    INC_DATA,
					    DEC_DATA,
					    READ_STDIN,
					    WRITE_STDOUT,
					    JUMP_IF_DATA_ZERO,
					    JUMP_IF_DATA_NOT_ZERO
					  };
					  
					  // Every op has a single numeric argument. For JUMP_* ops it's the offset to
					  // which a jump should be made; for all other ops, it's the number of times the
					  // op is to be repeated.
					  struct BfOp {
					    BfOp(BfOpKind kind_param, size_t argument_param)
					        : kind(kind_param), argument(argument_param) {}
					  
					    BfOpKind kind = BfOpKind::INVALID_OP;
					    size_t argument = 0;
					  };
					  ```
				- As planned, the main interpreter loop becomes:
					- ```c++
					  switch (kind) {
					  case BfOpKind::INC_PTR:
					    dataptr += op.argument;
					    break;
					  case BfOpKind::DEC_PTR:
					    dataptr -= op.argument;
					    break;
					  case BfOpKind::INC_DATA:
					    memory[dataptr] += op.argument;
					    break;
					  case BfOpKind::DEC_DATA:
					    memory[dataptr] -= op.argument;
					    break;
					  // [...] etc.
					  ```
					- For JUMP instructions, the numeric argument is the offset to jump to;
					- For other ops, it's the time of the repeated times of the op
			- Optimized interpreter - take 3
				- Add a few more operation kinds for encoding common loops:
					- ```c++
					  enum class BfOpKind {
					    INVALID_OP = 0,
					    INC_PTR,
					    DEC_PTR,
					    INC_DATA,
					    DEC_DATA,
					    READ_STDIN,
					    WRITE_STDOUT,
					    LOOP_SET_TO_ZERO,
					    LOOP_MOVE_PTR,
					    LOOP_MOVE_DATA,
					    JUMP_IF_DATA_ZERO,
					    JUMP_IF_DATA_NOT_ZERO
					  };
					  ```
				- [-] reduce the current memory location to 0
					- ```c++
					  std::vector<BfOp> optimize_loop(const std::vector<BfOp>& ops,
					                          size_t loop_start) {
					    std::vector<BfOp> new_ops;
					  
					    if (ops.size() - loop_start == 2) {
					      BfOp repeated_op = ops[loop_start + 1];
					      if (repeated_op.kind == BfOpKind::INC_DATA ||
					  repeated_op.kind == BfOpKind::DEC_DATA) {
					        new_ops.push_back(BfOp(BfOpKind::LOOP_SET_TO_ZERO, 0));
					  
					    // [...]
					  ```
					- Detect if the [] only contains + or -
					- ```c++
					  case BfOpKind::LOOP_SET_TO_ZERO:
					    memory[dataptr] = 0;
					    break;
					  ```
				- [-<<<+>>>] copy the value of memory location 1 to memory location 2
		- Compilers vs interpreters:
			- > gcc would be the canonical example of this: it transforms source code written in C (or C++) into assembly language for, say, Intel CPUs. But there are many other kinds of compilers: [Chicken](https://www.call-cc.org/) compiles Scheme into C; [Rhino](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino/JavaScript_Compiler) compiles Javascript to JVM bytecode; the [Clang frontend](https://clang.llvm.org/) compiles C++ to LLVM IR. CPython, the canonical Python implementation compiles Python source code [to bytecode](http://eli.thegreenplace.net/2010/06/30/python-internals-adding-a-new-statement-to-python), and so on. In general, the term [Bytecode](https://en.wikipedia.org/wiki/Bytecode) refers to any intermediate representation / virtual instruction set designed for efficient interpretation.
			- > where each instruction has a single argument. optinterp3 first __compiles__ BF to this bytecode, and only then executes the bytecode. So if we squint a bit, there's a JIT compiler here already - with the caveat that the compilation target is not executable machine code but rather this specialized bytecode.