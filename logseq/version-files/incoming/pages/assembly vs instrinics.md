---
title: assembly vs instrinics
---

- There are several ways to have low-level control over the generated code:
	 - Intrinsics are functions that the compiler provides. An intrinsic function has the appearance of a function call in C or C++, but compilation replaces the intrinsic by a specific sequence of low-level instructions.

	 - Note:
		 - Arm compilers recognize Arm intrinsics, but are not guaranteed to work with any third-party compiler toolchains.

		 - Inline assembly lets you write assembly instructions directly in your C/C++ code, without the overhead of a function call.

		 - Calling assembly functions from C/C++ lets you write standalone assembly code in a separate source file. This code is assembled separately to the C/C++ code, and then integrated at link time.

- Further reading:
	 - https://docs.microsoft.com/en-us/cpp/intrinsics/compiler-intrinsics-and-assembly-language?view=msvc-160
