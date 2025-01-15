- #[[Toy tutorial for MLIR]]
- ## Introduction: Multi-Level Intermediate Representation
- MLIR assembly for Toy transpose operations:
	- ```
	  %t_tensor = "toy.transpose"(%tensor) {inplace = true} : (tensor<2x3xf64>) -> 
	  tensor<3x2xf64> loc("example/file/path":12:1)
	  ```
- What does mlir-opt do? #card
  collapsed:: true
	- Check the syntax of the MLIR code.
	- Optimization and transformations
		- convert to other dialect
	- debugging
	- custom passes
- ### Opaque API
- use mlir-opt for the toy transpose without registering toy related dialects:
	- ```
	  func.func @toy_func(%tensor: tensor<2x3xf64>) -> tensor<3x2xf64> {
	    %t_tensor = "toy.transpose"(%tensor) { inplace = true } : (tensor<2x3xf64>) -> tensor<3x2xf64>
	    return %t_tensor : tensor<3x2xf64>
	  }
	  ```
- ## Defining a Toy Dialect
- What is the double colon ('::') in mlir::Dialect? #card
  collapsed:: true
	- use to access members (functions, variables or types) under a class or namespace.
- What is the namespace in C++? #card
  collapsed:: true
	- declarative region to provide a identifier for the variables or functions defined inside.
	- For example:
		- **Defining a Namespace**:
		- ```c++
		  namespace MyNamespace {
		    int myFunction() {
		        return 42;
		    }
		  }
		  ```
		- **Accessing Members of a Namespace**:
		  Using the scope resolution operator `::`:
		- ```c++
		  int x = MyNamespace::myFunction();
		  ```
- What is the public key word means? #card
	- ```c++
	  class ToyDialect : public mlir::Dialect {
	  public:
	    explicit ToyDialect(mlir::MLIRContext *ctx);
	    static llvm::StringRef getDialectNamespace() { return "toy"; }
	    void initialize();
	  };
	  ```
	- In the class defination:
	  collapsed:: true
		- Here's a brief overview of the three main access specifiers:
		- **public**: Members declared as public are accessible from any function or method, both inside and outside the class. In the provided code, the constructor `ToyDialect`, the method `getDialectNamespace`, and the method `initialize` are all publicly accessible.
		- **private**: Members declared as private are only accessible within the class itself. They cannot be accessed (or viewed) from outside the class. This is the default access level if no access specifier is provided.
		- **protected**: Members declared as protected are accessible within the class and its derived (or subclassed) classes. They are not accessible from outside these classes, similar to private members.
	- In the context of inheritance level:
	  collapsed:: true
		- **public inheritance**: When you inherit publicly, as in the case of `class ToyDialect : public mlir::Dialect`, it means that the public members of the base class (`mlir::Dialect`) will remain public in the derived class (`ToyDialect`). Similarly, protected members of the base class will remain protected in the derived class. Private members of the base class are not accessible directly from the derived class, regardless of the type of inheritance.
		- Essentially, with public inheritance, the derived class is saying, "I am a kind of the base class." It's the most common form of inheritance.
		- **protected inheritance**: With this form of inheritance, public and protected members of the base class become protected members of the derived class.
		- **private inheritance**: With private inheritance, both public and protected members of the base class become private members of the derived class.
- What is the explicit key word means?#card
  collapsed:: true
	- explicit is used to mark a constructor of a class as non-converting. Meaning the compiler should not use that constructor for implicit conversions.
	- ```c++
	  class Box {
	  public:
	      Box(int w) {
	          width = w;
	      }
	  private:
	      int width;
	  };
	  Box myBox = 10;  // Implicit conversion from int to Box
	  ```
	- With explicit key word:
	- ```c++
	  class Box {
	  public:
	      explicit Box(int w) {
	          width = w;
	      }
	  private:
	      int width;
	  };
	  // Box myBox = 10;  // This will cause a compile error now
	  Box myBox(10);  // This is fine
	  ```
-
- ## Defining Toy Operations
- ```c++
  //===----------------------------------------------------------------------===//
  // TransposeOp
  //===----------------------------------------------------------------------===//
  // build class is typically called when an instance of TransposeOp is created in MLIR framework
  void TransposeOp::build(mlir::OpBuilder &builder, mlir::OperationState &state,
                          mlir::Value value) {
    state.addTypes(UnrankedTensorType::get(builder.getF64Type()));
    state.addOperands(value);
  }
  
  mlir::LogicalResult TransposeOp::verify() {
    auto inputType = llvm::dyn_cast<RankedTensorType>(getOperand().getType());
    auto resultType = llvm::dyn_cast<RankedTensorType>(getType());
    if (!inputType || !resultType)
      return mlir::success();
  
    auto inputShape = inputType.getShape();
    if (!std::equal(inputShape.begin(), inputShape.end(),
                    resultType.getShape().rbegin())) {
      return emitError()
             << "expected result shape to be a transpose of the input";
    }
    return mlir::success();
  }
  ```
- `build`:
	- `build` class is typically called when an instance of `TransposeOp` is created.
	- parameter walk through:
		- `mlir::OpBuilder`:
			- example:
			- 提供了创建Op的函数接口，提供了一些创建Op的方法。
			- ```c++
			  mlir::OpBuilder builder(context);
			  auto location = builder.getUnknownLoc();
			  auto integerType = builder.getIntegerType(32);
			  auto constantOp = builder.create<mlir::ConstantOp>(location, integerType, builder.getI32IntegerAttr(42));
			  ```
		- `mlir::OperationState`
			- 包含了创建一个op的所有需要的参数，有点类似于nbperf里面的OpSpec
			- example:
			- ```c++
			  mlir::MLIRContext context;
			  mlir::OpBuilder builder(&context);
			  mlir::Location location = builder.getUnknownLoc();
			  
			  mlir::OperationState state(location, "my_custom_operation");
			  state.operands = { /* operands go here */ };
			  state.attributes = { /* attributes go here */ };
			  
			  mlir::Operation *operation = mlir::Operation::create(state);
			  ```
		- `mlir::Value`
			- contains the operand for the `TransposeOp`, by operand,应该对应的就是这个Op的输入。
	- in summary, this `build` function takes an `OpBuilder` to construct the operation, an `OperationState` to hold the operation's information, and a `Value` to be used as the operation's operand. It then adds an `UnrankedTensorType` of `F64` to the operation's types and adds the `value` as an operand of the operation.
- `verify`
	- provide sanity check for a given operator
- What is a `mlir::LogicalResult`? #card
	- Essentially a boolean value.
	- `mlir::LogicalResult` is a type used in the MLIR (Multi-Level Intermediate Representation) framework to represent the success or failure of an operation
	- ```c++
	  mlir::LogicalResult doSomething() {
	    if (someCondition) {
	      return mlir::failure();
	    }
	    // Do something...
	    return mlir::success();
	  }
	  ```
- What is `RankedTensorType`? #card
	- In MLIR, `RankedTensorType` is used to represent tensors where the number of dimensions is known at compile time. This is in contrast to `UnrankedTensorType`, which is used to represent tensors where the number of dimensions is not known until runtime.
	- ```c++
	  mlir::MLIRContext context;
	  int64_t shape[] = {2, 3};
	  auto elementType = mlir::FloatType::getF32(&context);
	  auto tensorType = mlir::RankedTensorType::get(shape, elementType);
	  ```
	- In this example, a `RankedTensorType` is created with a shape of `[2, 3]` (meaning it's a 2x3 tensor) and an element type of `float`.
- For the given code, why is `TransposeOp::` not needed for `getOperand()` function? #card
  card-last-interval:: -1
  card-repeats:: 1
  card-ease-factor:: 2.5
  card-next-schedule:: 2024-02-27T16:00:00.000Z
  card-last-reviewed:: 2024-02-27T12:02:23.031Z
  card-last-score:: 1
  ```c++
  mlir::LogicalResult TransposeOp::verify() {
  card-repeats:: 1
  card-ease-factor:: 2.5
  card-next-schedule:: 2024-02-27T16:00:00.000Z
  card-last-reviewed:: 2024-02-27T12:02:23.031Z
  card-last-score:: 1
    auto inputType = llvm::dyn_cast<RankedTensorType>(getOperand().getType());
    auto resultType = llvm::dyn_cast<RankedTensorType>(getType());
    if (!inputType || !resultType)
      return mlir::success();
  
    auto inputShape = inputType.getShape();
    if (!std::equal(inputShape.begin(), inputShape.end(),
                    resultType.getShape().rbegin())) {
      return emitError()
             << "expected result shape to be a transpose of the input";
    }
    return mlir::success();
  }
  
  ```
	- That is because 这个函数是在`TransposeOp`的member function中调用，在这种场景中，可以直接调用其他member function，而不需要加`TransposeOp::`这个prefix。
- What does `getOperand` function do? #card
	- The `getOperand()` function is a method provided by the base `Operation` class in MLIR, which `TransposeOp` is derived from. This method returns an operand of the operation, ==which is an input to the operation==. The type of the return value is `mlir::Value`, which represents a SSA value that can be used as an operand of an operation.
- What does `getType` function do? #card
	- ==Get the operation's output or result.==
	- when `getType()` is called on an `mlir::Value` instance, it returns the type of the value.
	- In MLIR, every `Value` has a type, which is an instance of a subclass of `mlir::Type`. The `getType()` method allows you to retrieve this type. The returned type can be any of the types supported by MLIR, such as `IntegerType`, `FloatType`, `VectorType`, `TensorType`, `RankedTensorType`, etc.
	- As an example:
	- ```c++
	  mlir::Value val = // ...
	  mlir::Type type = val.getType();
	  ```
-