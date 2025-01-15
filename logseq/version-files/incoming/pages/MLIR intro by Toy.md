---
title: MLIR intro by Toy
---

- id:: 6226c9bb-faa5-4887-ab0c-6dfb07e69238
- MLIR video tutorial
	- {{youtube https://www.youtube.com/watch?v=Y4SvqTtOIDk}}
	- Build a Toy language and its compiler using MLIR
		- {{youtube-timestamp 359}}
			- High level language:
				- Toy is a mix of scalar and array computations
		- {{youtube-timestamp 600}}
			- All about Dialects
				- MLIR allows every level of abstraction to be modeled as a Dialect
				- For example, OpenMP dialects can be re-used to get acceleration on certain hardware without the effort of rewriting the whole dialect again.
				  So toy dialect can also reuse OpenMP dialects if it want some parallel computing acceleration just like the Fortran uses OpenMP dialects.
		- {{youtube-timestamp 666}}
			- Operation in MLIR
				- operations share the same extensible structure
				- no predefined set of instructions
				- ![image.png](../assets/image_1646705443237_0.png){:height 85, :width 597}
					- This is [[textual IR]], which makes the [[serialization and deserialization]] easy in MLIR
					- Details of the operation:
						- ![image.png](../assets/image_1646705752127_0.png){:height 227, :width 615}
		- {{youtube-timestamp 973}}
			- ![image.png](../assets/image_1646706020434_0.png){:height 213, :width 572}
			- Regions in MLIR
				- Operation contains Regions
					- Regions contains blocks which can be a combination of operations which contains regions
				- Basic Blocks are a list of operations: the IR structure is recursively nested
				- Regions are very similar to function call, but can refer SSA values defined outside
				- SSA values defined inside don't escape
		- {{youtube-timestamp 1231}}
			- Dialects in MLIR
				- like the library in C++
					- Contains types you define and operations operating on this type
				- Example:
					- {{youtube-timestamp 1575}}
			- {{youtube-timestamp 1679}}
				- Toy dialect: the dialect
					- Declaratively specified in TableGen
						- We can generate a markdown format readme from this
						- Will generate a C++ class from the declaratively
			- {{youtube-timestamp 1754}}
				- Operations in Toy
					- `dense` is build-in dtype to store continuous data
			- {{youtube-timestamp 1982}}
				- C++ code generated form the TableGen
			- {{youtube-timestamp 2059}}
				- Traits in MLIR
				-
		-
- [Chapter1: Toy Language and AST](Chapter1: Toy Language and AST)
  collapsed:: true
	- > This tutorial will be illustrated with a toy language that we’ll call “Toy” (naming is hard…). Toy is a tensor-based language that allows you to define functions, perform some math computation, and print results.
	- ### Target
	  collapsed:: true
		- In this tutorial, we will learn to print the AST generated using MLIR after compiling the chp1.toy file.
	- ### Base knowledge:
	  collapsed:: true
		- [[lexer]] [[Abstract Syntax Tree]]
	- ### Line by Line analysis
		- The AST
			- https://mlir.llvm.org/docs/Tutorials/Toy/Ch-1/#the-ast
			- The position of the code is represented by
				- ```
				      Proto 'multiply_transpose' @test/Examples/Toy/Ch1/ast.toy:4:1'
				  ```
				- 4:1 means the following idx of line: idx  in line. Which is the first character in the 4th line
			- Function declaration
				- ```
				    Function 
				      Proto 'multiply_transpose' @test/Examples/Toy/Ch1/ast.toy:4:1'
				      Params: [a, b]
				      Block {
				        Return
				          BinOp: * @test/Examples/Toy/Ch1/ast.toy:5:25
				            Call 'transpose' [ @test/Examples/Toy/Ch1/ast.toy:5:10
				              var: a @test/Examples/Toy/Ch1/ast.toy:5:20
				            ]
				            Call 'transpose' [ @test/Examples/Toy/Ch1/ast.toy:5:25
				              var: b @test/Examples/Toy/Ch1/ast.toy:5:35
				            ]
				      } // Block
				  ```
				- declare the function of multiply transpose
				- refer to [[Abstract Syntax Tree]] , exactly the same as the graph
					- binop
						- call transpose on a
						- call transpose on b
			- VarDecl
				- 注意与传入函数的形参的形式不同：
					- ```
					  var: a @test/Examples/Toy/Ch1/ast.toy:5:20
					  ```
				- 1. `VarDecl`关键字，vs `var:` 关键字
				  2. `<>` 标识符
				- ```
				  VarDecl a<> @test/Examples/Toy/Ch1/ast.toy:11:3
				          Literal: <2, 3>[ <3>[ 1.000000e+00, 2.000000e+00, 3.000000e+00], <3>[ 4.000000e+00, 5.000000e+00, 6.000000e+00]] @test/Examples/Toy/Ch1/ast.toy:11:11
				  ```
	-
	-
- [[Chapter 2: Emitting Basic MLIR]]
	- > multiple frontends end up reimplementing significant pieces of infrastructure to support the need for these analyses and transformation. ^^MLIR^^ addresses this issue by being ^^designed for extensibility^^ . As such, there are few pre-defined instructions (operations in MLIR terminology) or types.
	- # Intro to MLIR
	- # Interfacing with MLIR
		- Example assembly for the Toy transpose: [[operation in MLIR]]
			- ^^**Kind of like a function**^^
			- Shown here is a general form of an operation.
				- Which are modeled using a small set of concepts:
					- A name for the operation.
					  A list of SSA operand values.
					  A list of attributes.
					  A list of types for result values.
					  A source location for debugging purposes.
					  A list of successors [[blocks in MLIR]] (for branches, mostly).
					  A list of [[regions in MLIR]] (for structural operations like functions).
			- ```c++
			  %t_tensor = "toy.transpose"(%tensor) {inplace = true} : (tensor<2x3xf64>) -> tensor<3x2xf64> loc("example/file/path":12:1)
			  ```
				- `%tensor`
					- > MLIR guarantees identifiers ^^never collide with keywords by prefixing identifiers^^ with a sigil (e.g. %, #, @, ^, !).
					- In the context of Toy, limit to SSA values. [[SSA]]
				- `"toy.transpose"`
					- > The name of the operation. It is expected to be a unique string, with the namespace of the dialect prefixed before the “.”. ^^This can be read as the transpose operation in the toy dialect.^^
				- `(%tensor)`
					- > A list of zero or more input operands (or arguments), which are SSA values defined by other operations or referring to block arguments.
				- `(tensor<2x3xf64>) -> tensor<3x2xf64>`
					- > This refers to the type of the operation in a functional form, spelling the types of the arguments in parentheses and the type of the return values afterward.
				- `loc("example/file/path":12:1)`
					- > This is the location in the source code from which this operation originated.
					- > To provide an illustration: If a transformation replaces an operation by another, that new operation must still have a location attached. This makes it possible to track where that operation came from.
		- ## Opaque API
			- DONE 没看懂
			  :LOGBOOK:
			  CLOCK: [2022-03-07 Mon 21:10:02]--[2022-03-07 Mon 21:10:02] =>  00:00:00
			  :END:
			- > MLIR is designed to allow all IR elements, such as attributes, operations, and types, to be customized.
			- Example:
				- we could place our Toy operation from above into an .mlir file and round-trip through mlir-opt without registering any dialect:
					- ```c++
					  func @toy_f	unc(%tensor: tensor<2x3xf64>) -> tensor<3x2xf64> {
					    %t_tensor = "toy.transpose"(%tensor) { inplace = true } : (tensor<2x3xf64>) -> tensor<3x2xf64>
					    return %t_tensor : tensor<3x2xf64>
					  }
					  ```
	- # Defining a Toy Dialect
		- C++ definition of a dialect:
			- ```c++
			  /// This is the definition of the Toy dialect. A dialect inherits from
			  /// mlir::Dialect and registers custom attributes, operations, and types. It can
			  /// also override virtual methods to change some general behavior, which will be
			  /// demonstrated in later chapters of the tutorial.
			  class ToyDialect : public mlir::Dialect {
			  public:
			    explicit ToyDialect(mlir::MLIRContext *ctx);
			  
			    /// Provide a utility accessor to the dialect namespace.
			    static llvm::StringRef getDialectNamespace() { return "toy"; }
			  
			    /// An initializer called from the constructor of ToyDialect that is used to
			    /// register attributes, operations, types, and more within the Toy dialect.
			    void initialize();
			  };
			  ```
			- This is the C++ definition of a dialect, MLIR also supports defining dialect via [[TableGen in MLIR]]
			- DONE [[explicit in C++]]
		- TableGen version of a dialect:
			- It also enables easy generation of dialect documentation, which can be described directly alongside the dialect. In this declarative format, the toy dialect would be specified as:
			- ```C++
			  // Provide a definition of the 'toy' dialect in the ODS framework so that we
			  // can define our operations.
			  def Toy_Dialect : Dialect {
			    // The namespace of our dialect, this corresponds 1-1 with the string we
			    // provided in `ToyDialect::getDialectNamespace`.
			    let name = "toy";
			  
			    // A short one-line summary of our dialect.
			    let summary = "A high-level dialect for analyzing and optimizing the "
			                  "Toy language";
			  
			    // A much longer description of our dialect.
			    let description = [{
			      The Toy language is a tensor-based language that allows you to define
			      functions, perform some math computation, and print results. This dialect
			      provides a representation of the language that is amenable to analysis and
			      optimization.
			    }];
			  
			    // The C++ namespace that the dialect class definition resides in.
			    let cppNamespace = "toy";
			  }
			  ```
			- TableGen'nerated file:
				- [[C++: Difference between private, public and protected inheritance]]
				- ```c++
				  namespace mlir {
				  namespace toy {
				  
				  class ToyDialect : public ::mlir::Dialect {
				    explicit ToyDialect(::mlir::MLIRContext *context)
				      : ::mlir::Dialect(getDialectNamespace(), context,
				        ::mlir::TypeID::get<ToyDialect>()) {
				      
				      initialize();
				    }
				  
				    void initialize();
				    friend class ::mlir::MLIRContext;
				  public:
				    ~ToyDialect() override;
				    static constexpr ::llvm::StringLiteral getDialectNamespace() {
				      return ::llvm::StringLiteral("toy");
				    }
				  };
				  } // namespace toy
				  } // namespace mlir
				  DECLARE_EXPLICIT_TYPE_ID(::mlir::toy::ToyDialect)
				  ```
				- The upper dialect can be load in `MLIRContext` using:
					- `  context.loadDialect<ToyDialect>();`
					- > By default, an MLIRContext only loads the [[Builtin Dialect]], which provides a few core IR components, meaning that other dialects, such as our Toy dialect, must be explicitly loaded.
	- # Defining Toy Operations
		- Constant value representation:
			- ```c++
			   %4 = "toy.constant"() {value = dense<1.0> : tensor<2x3xf64>} : () -> tensor<2x3xf64>
			  ```