- 如果有多个[[Function in MLIR]]，pass如何遍历？
	- ```
	  // PrintFunctionNamesPass.cpp
	  #include "mlir/IR/Builders.h"
	  #include "mlir/IR/Function.h"
	  #include "mlir/IR/Module.h"
	  #include "mlir/Pass/Pass.h"
	  
	  using namespace mlir;
	  
	  namespace {
	  struct PrintFunctionNamesPass : public PassWrapper<PrintFunctionNamesPass, OperationPass<ModuleOp>> {
	    void runOnOperation() override {
	      ModuleOp module = getOperation();
	  
	      // 遍历模块中的每个函数
	      module.walk([](FuncOp func) {
	        // 打印函数名称
	        llvm::outs() << "Function name: " << func.getName() << "\n";
	      });
	    }
	  };
	  } // namespace
	  
	  // 注册Pass
	  namespace mlir {
	  void registerPrintFunctionNamesPass() {
	    PassRegistration<PrintFunctionNamesPass>(
	        "print-function-names", "Print the names of all functions in the module");
	  }
	  } // namespace mlir
	  ```
	- output:
		- ```
		  Function name: extracted_func
		  Function name: foo
		  ```
- [[Function in MLIR]]在[[Module in MLIR]] 中的位置？
	- `func`操作（即函数定义）通常是顶层的，并且直接包含在`module`操作之下。MLIR中的`module`操作相当于一个容器，包含了所有的顶层操作，包括函数、全局变量、类型定义等。
	- MLIR目前不支持嵌套的函数定义，即一个函数内部不能包含另一个函数定义。但是，MLIR支持嵌套的模块定义，这意味着一个模块可以包含另一个模块。
	- 例如：
		- ```
		  module {
		    func @foo(%arg0: i32, %arg1: i32) -> i32 {
		      %0 = addi %arg0, %arg1 : i32
		      return %0 : i32
		    }
		  
		    func @bar(%arg0: i32) -> i32 {
		      %0 = call @foo(%arg0, %arg0) : (i32, i32) -> i32
		      return %0 : i32
		    }
		  }
		  ```
	- 中间的foo可以放到另外一个module里面：
		- ```
		  module {
		    module @nested_module {
		      func @nested_foo(%arg0: i32, %arg1: i32) -> i32 {
		        %0 = addi %arg0, %arg1 : i32
		        return %0 : i32
		      }
		    }
		  
		    func @outer_foo(%arg0: i32, %arg1: i32) -> i32 {
		      %0 = addi %arg0, %arg1 : i32
		      return %0 : i32
		    }
		  }
		  ```
-
- [[Module in MLIR]] 嵌套的用处？
	- ### 1. 层次化组织代码
		- 嵌套模块允许将代码按照层次结构组织起来。这样可以更好地管理大型代码库，清晰地表示不同的逻辑层次或组件。
		- ```
		  module {
		  module @layer1 {
		    func @foo() {
		      // Layer 1 functionality
		    }
		  }
		  
		  module @layer2 {
		    func @bar() {
		      // Layer 2 functionality
		    }
		  }
		  }
		  ```
	- ### 2. 模块化设计
		- 通过嵌套模块，可以将不同的功能或组件封装在独立的模块中，便于代码的复用和维护。这种模块化设计有助于分离关注点，使得代码更易于理解和修改。
		- ```
		  module {
		  module @math_operations {
		    func @add(%a: i32, %b: i32) -> i32 {
		      %result = addi %a, %b : i32
		      return %result : i32
		    }
		  }
		  
		  module @string_operations {
		    func @concat(%a: memref<10xi8>, %b: memref<10xi8>) -> memref<20xi8> {
		      // Concatenation logic
		    }
		  }
		  }
		  ```
	- ### 3. 隔离命名空间
		- 嵌套模块可以用于创建独立的命名空间，防止不同模块中的符号发生冲突。这在大型项目中尤其有用，可以避免命名冲突和意外的符号覆盖。
		- ```
		  module {
		  module @math {
		    func @add(%a: i32, %b: i32) -> i32 {
		      %result = addi %a, %b : i32
		      return %result : i32
		    }
		  }
		  
		  module @string {
		    func @add(%a: memref<10xi8>, %b: memref<10xi8>) -> memref<20xi8> {
		      // Different add function for strings
		    }
		  }
		  }
		  ```
	- ### 4. 独立编译和优化
		- 嵌套模块可以独立编译或优化，这意味着在一个复杂的编译流程中，可以单独处理某些模块。例如，可以对某些模块应用特定的优化Pass，而对其他模块应用不同的优化策略。
		- ```
		  module {
		  module @fast_math {
		    // Functions that will be optimized for speed
		  }
		  
		  module @accurate_math {
		    // Functions that will be optimized for accuracy
		  }
		  }
		  ```
- [[Context in MLIR]] 的作用是什么？
	- 包含了Dialect中定义的Type和Operation等信息。包括了：
		- **符号表**：存储和管理所有的符号，确保符号在整个编译过程中是唯一的。
		- **类型表**：存储和管理所有的类型，确保类型在整个编译过程中是唯一的。
	- 还有一些编译中的全局配置选项
	- 还有内存管理。
	- ```
	    // 创建一个 MLIRContext
	    MLIRContext context;
	  
	    // 注册标准操作Dialect
	    context.getOrLoadDialect<StandardOpsDialect>();
	  
	    // 创建一个模块
	    OwningOpRef<ModuleOp> module = ModuleOp::create(Location::unknown(&context));
	  
	  ```
- [[MLIR Region]]是什么？
	- `Region` 是一种容器类型，用于表示==一组有序的操作（operations）==。它是 MLIR IR 结构中的一个重要组成部分，用于构建复杂的控制流和作用域。
	- example:
	  collapsed:: true
		- ```
		  #include "mlir/IR/MLIRContext.h"
		  #include "mlir/IR/Module.h"
		  #include "mlir/IR/Builders.h"
		  #include "mlir/IR/Types.h"
		  #include "mlir/IR/Attributes.h"
		  #include "mlir/IR/Operation.h"
		  #include "mlir/IR/Region.h"
		  #include "mlir/Dialect/StandardOps/IR/Ops.h"
		  #include "mlir/Dialect/Func/IR/FuncOps.h"
		  
		  using namespace mlir;
		  
		  int main() {
		    // 创建一个 MLIRContext
		    MLIRContext context;
		    context.getOrLoadDialect<StandardOpsDialect>();
		    context.getOrLoadDialect<arith::ArithmeticDialect>();
		    context.getOrLoadDialect<func::FuncDialect>();
		  
		    // 创建一个模块
		    OwningOpRef<ModuleOp> module = ModuleOp::create(Location::unknown(&context));
		  
		    // 使用 Builder 创建操作
		    OpBuilder builder(module->getBody());
		  
		    // 创建一个函数类型
		    auto funcType = builder.getFunctionType(builder.getIntegerType(32), {});
		  
		    // 创建一个函数操作
		    auto func = builder.create<FuncOp>(builder.getUnknownLoc(), "myFunc", funcType);
		  
		    // 将函数添加到模块中
		    module->push_back(func);
		  
		    // 创建一个 Region 并将其添加到函数操作中
		    auto &entryRegion = func.getBody();
		    entryRegion.push_back(new Block());
		  
		    // 获取函数的入口块
		    Block *entryBlock = &entryRegion.front();
		    builder.setInsertionPointToStart(entryBlock);
		  
		    // 在入口块中创建一个常量操作
		    auto constantOp = builder.create<arith::ConstantOp>(builder.getUnknownLoc(), builder.getI32IntegerAttr(42));
		  
		    // 创建一个返回操作
		    builder.create<func::ReturnOp>(builder.getUnknownLoc(), constantOp.getResult());
		  
		    // 打印模块
		    module->print(llvm::outs());
		  
		    return 0;
		  }
		  ```
	- output:
		- ```
		  module {
		    func @myFunc() -> i32 {
		      %0 = arith.constant 42 : i32
		      return %0 : i32
		    }
		  }
		  ```
	- `func @myFunc() -> i32 { ... }` 包含一个 `Region`，表示函数的主体。
- SafeRunOnModule和SafeRunOnFunc的区别是什么，分别是什么用法？
	- Defined in VPUX:
		- ```
		  
		  //
		  // FunctionPass
		  //
		  
		  class FunctionPass : public mlir::OperationPass<mlir::func::FuncOp> {
		  public:
		      using mlir::OperationPass<mlir::func::FuncOp>::OperationPass;
		  
		  protected:
		      void initLogger(Logger log, StringLiteral passName);
		  
		  protected:
		      virtual void safeRunOnFunc() = 0;
		  
		  protected:
		      Logger _log = Logger::global();
		  
		  private:
		      void runOnOperation() final;
		  };
		  
		  //
		  // ModulePass
		  //
		  
		  class ModulePass : public mlir::OperationPass<mlir::ModuleOp> {
		  public:
		      using mlir::OperationPass<mlir::ModuleOp>::OperationPass;
		  
		  protected:
		      void initLogger(Logger log, StringLiteral passName);
		  
		  protected:
		      virtual void safeRunOnModule() = 0;
		  
		  protected:
		      Logger _log = Logger::global();
		  
		  private:
		      void runOnOperation() final;
		  };
		  ```
	- 基本上就是继承了FunctionPass和ModulePass
		- FunctionPass:
			- ```
			  namespace {
			    struct MyFunctionPass : public FunctionPass {
			      void safeRunOnFunc() override {
			        // Function pass implementation
			        mlir::func::FuncOp func = getOperation();
			        // Example: Iterate over blocks in the function
			        for (auto &block : func.getBlocks()) {
			          // Process each block
			        }
			      }
			    };
			  }
			  
			  std::unique_ptr<mlir::Pass> createMyFunctionPass() {
			    return std::make_unique<MyFunctionPass>();
			  }
			  ```
		- ModulePass:
			- ```
			  namespace {
			    struct MyModulePass : public ModulePass {
			      void safeRunOnModule() override {
			        // Module pass implementation
			        mlir::ModuleOp module = getOperation();
			        // Example: Iterate over functions in the module
			        for (auto func : module.getOps<mlir::func::FuncOp>()) {
			          // Process each function
			        }
			      }
			    };
			  }
			  
			  std::unique_ptr<mlir::Pass> createMyModulePass() {
			    return std::make_unique<MyModulePass>();
			  }
			  ```
- 在做 [[operation in MLIR]] 的遍历或者匹配的时候，会匹配到operator内部body里面定义的op吗？
	- 不会。。因为只有ModulePass和FunctionPass，需要在内部递归
	- 非递归遍历：
		- ```
		  void traverseTopLevelOps(mlir::ModuleOp module) {
		    for (auto op : module.getOps()) {
		      // 只处理顶层的操作，不进入操作内部的body
		      llvm::outs() << "Top-level operation: " << op->getName() << "\n";
		    }
		  }
		  ```
	- 递归遍历：
		- ```
		  void traverseAllOps(mlir::ModuleOp module) {
		    module.walk([](mlir::Operation *op) {
		      // 处理所有操作，包括子操作
		      llvm::outs() << "Operation: " << op->getName() << "\n";
		    });
		  }
		  ```
	- MatchAndRewrite的匹配顶层的操作：
		- ```
		  struct MyPattern : public mlir::OpRewritePattern<MyOp> {
		    using mlir::OpRewritePattern<MyOp>::OpRewritePattern;
		  
		    mlir::LogicalResult matchAndRewrite(MyOp op,
		                                        mlir::PatternRewriter &rewriter) const override {
		      // 在这里匹配和重写 MyOp，但不进入 MyOp 内部的body
		      if (/* some condition */) {
		        // Perform some transformation
		        return mlir::success();
		      }
		      return mlir::failure();
		    }
		  };
		  ```
	- 递归匹配：
		- ```
		  struct MyRecursivePattern : public mlir::OpRewritePattern<MyOp> {
		    using mlir::OpRewritePattern<MyOp>::OpRewritePattern;
		  
		    mlir::LogicalResult matchAndRewrite(MyOp op,
		                                        mlir::PatternRewriter &rewriter) const override {
		      // 递归处理 MyOp 及其内部的操作
		      op->walk([&](mlir::Operation *nestedOp) {
		        if (auto targetOp = llvm::dyn_cast<TargetOp>(nestedOp)) {
		          // Perform some transformation on nested targetOp
		        }
		      });
		      return mlir::success();
		    }
		  };
		  ```
- 如何在 [[Function in MLIR]] 中添加constant attribute？
	- ```
	  mlir::func::FuncOp func = ...; // 获取当前函数操作
	  func.setAttr("my_constant_attr", myAttr);
	  ```
	- ```
	  func @my_function() attributes {my_constant_attr = "my_constant_value"} {
	    // 函数体
	  }
	  ```
- [[Block in MLIR]] 和 [[MLIR Region]] 的区别是什么？
	- ```
	  func @example() {
	    ^bb0:  // Block 0
	      %0 = constant 42 : i32
	      br ^bb1(%0 : i32)
	  
	    ^bb1(%1: i32):  // Block 1
	      %2 = addi %1, %1 : i32
	      return
	  }
	  ```
	- 在这个例子中：
		- `func @example()` 是一个函数操作（`func::FuncOp`）。
		- 这个函数包含一个 `Region`，表示函数的主体。
		- 函数的 `Region` 包含两个 `Block`（`^bb0` 和 `^bb1`）。
		- 每个 `Block` 包含一组操作。
- [[Module in MLIR]] 只有一个吗？
	- 通常情况下，一个 MLIR 文件或程序会有一个顶层的 `Module` 操作
	- 在 MLIR 中，`TableGen` 用于描述操作（Ops）、类型（Types）、属性（Attributes）等。
- [[TableGen]]是什么? TableGen通常需要写哪些东西？
	- `TableGen` 的主要目的是通过声明性语法来描述复杂的数据结构和模式，然后自动生成相应的 C++ 代码。这减少了手工编写和维护代码的工作量，提高了代码的一致性和安全性。
- Lowering通常需要写什么？举个具体的例子？
- 集成测试这里是什么意思？只是register吗？
  ```
  void registerPasses() {
    PassRegistration<OptimizePass>("optimize", "Optimize AI operations");
    PassRegistration<LoweringPass>("lowering", "Lower AI operations to standard MLIR");
    PassRegistration<CodeGenPass>("codegen", "Generate code from MLIR");
  }
  ```
- 在operation的定义中，这一段是什么意思：
  ```
      // 访问操作的输入和输出
      Value lhs() { return getOperand(0); }
      Value rhs() { return getOperand(1); }
      Value result() { return getResult(); }
  
  ```
- 用builder和rewriter构建operator的区别？
	- ### 主要区别
		- **功能范围**：
			- **OpBuilder**：主要用于创建新操作，不会自动处理现有操作的替换或删除。
			- **PatternRewriter**：不仅可以创建新操作，还可以替换和删除现有操作，适用于模式匹配和重写。
		- **适用场景**：
			- **OpBuilder**：通常用于初始化操作和构建新操作序列的场景。
			- **PatternRewriter**：通常用于编写转换、优化 pass 和模式重写的场景。
		- **操作生命周期管理**：
			- **OpBuilder**：用户需要手动管理操作的生命周期，确保正确的插入点。
			- **PatternRewriter**：自动管理操作的生命周期，提供替换和删除功能，简化重写逻辑。
	- ### 选择使用哪一个
		- **初始化和构建新操作时**：使用 `OpBuilder`。
		- **进行转换和优化时**：使用 `PatternRewriter`。
- 什么时候module需要explicitly写出来module.push_back(funcOp)?
- loc有什么用，随便给会有什么问题？
- 有什么MLIR自带的功能能够在pass对所有的function call某一个修改的函数，但是不需要coder手动去写?
- module没有输入输出的概念，是一个类似于namespace的概念，对吗？
- module是operation的一种吗？
-