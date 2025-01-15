---
title: How to JIT - an introduction
---

- ((515a67fe-0f21-403d-8c70-b1c6612a7c3d))

- Reading notes of [How to JIT- an introduction](https://eli.thegreenplace.net/2013/11/05/how-to-jit-an-introduction/)
	 - copy and paste:
		 - JIT - create machine code, then run it
			 - I think that JIT technology is easier to explain when divided into two distinct phases:
				 - Phase 1: create [machine code](http://en.wikipedia.org/wiki/Machine_code) at program run-time.

				 - Phase 2: execute that machine code, also at program run-time.

		 - Running dynamically-generated code
			 - Getting machine code into memory is easy. But how to make it runnable, and then run it?
				 - `unsigned char[] code = {0x48, 0x89, 0xf8};` is basically `mov %rdi, %rax`

				 - My understanding is that the machine code much like a data, can be generated during the runtime and placed on the heap.

		 - Let's see some code
			 - 
```c++
long add4(long num) {
  return num + 4;
}
```

			 - Here's a first try (the full code with a Makefile is available in [this repo](https://github.com/eliben/libjit-samples)):

			 - 
```c++
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/mman.h>


// Allocates RWX memory of given size and returns a pointer to it. On failure,
// prints out the error and returns NULL.
void* alloc_executable_memory(size_t size) {
  void* ptr = mmap(0, size,
                   PROT_READ | PROT_WRITE | PROT_EXEC,
                   MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
  if (ptr == (void*)-1) {
    perror("mmap");
    return NULL;
  }
  return ptr;
}

void emit_code_into_memory(unsigned char* m) {
  unsigned char code[] = {
    0x48, 0x89, 0xf8,                   // mov %rdi, %rax
    0x48, 0x83, 0xc0, 0x04,             // add $4, %rax
    0xc3                                // ret
  };
  memcpy(m, code, sizeof(code));
}

const size_t SIZE = 1024;
typedef long (*JittedFunc)(long);

// Allocates RWX memory directly.
void run_from_rwx() {
  void* m = alloc_executable_memory(SIZE);
  emit_code_into_memory(m);

  JittedFunc func = m;
  int result = func(2);
  printf("result = %d\n", result);
}
```
				 - Use mmap to allocate a readable, writable and executable chunk of memory on the heap. (rwx-->alloc_executable_memory!!)

				 - Copy the machine code implementing add4 into this chunk.

				 - Execute code from this chunk by casting it to a function pointer and calling through it.
