---
title: Tracing just-in-time compilation
---

- Reference:
	 - [[Adventures in JIT compilation: Part 1 - an interpreter]]

	 - https://en.wikipedia.org/wiki/Tracing_just-in-time_compilation

- Questions:
	 - Is this just a special kind of compiler optimization?
		 - Compiler optimization might fail to detail the hot loops because the compiler is not able to profile the program.

	 - Why do we need JIT to compile this?
		 - Same as above.

- > Whereas method-based JIT compilers translate one method at a time to machine code, tracing JITs use frequently executed loops as their unit of compilation.

- For tracing jit compiler, we need to collect profiling information to detect hot loops.
	 - > A tracing JIT compiler goes through various phases at runtime. First, [profiling](https://en.wikipedia.org/wiki/Profiling_%28computer_programming%29) information for loops is collected. After a hot loop has been identified, a special [tracing](https://en.wikipedia.org/wiki/Tracing_%28software%29) phase is entered, which records all executed operations of that loop. This sequence of operations is called a trace. The trace is then optimized and compiled to machine code (trace). When this loop is executed again, the compiled trace is called instead of the program counterpart.

- Example:
	 - 
```c++
def square(x):
    return x * x

i = 0
y = 0
while True:
    y += square(i)
    if y > 100000:
        break
    i = i + 1
```

	 - trace for the program, inline the function call to the trace. (basically inline function calls and common compiler optimizations for tracing JIT)
		 - 
```c++
 loopstart(i1, y1)
 i2 = int_mul(i1, i1)		# x*x
 y2 = int_add(y1, i2)		# y += i*i
 b1 = int_gt(y2, 100000)
 guard_false(b1)
 i3 = int_add(i1, 1)		# i = i+1
 jump(i3, y2)
```
