- Lowering:
	- ```
	  struct LoweringPass : public PassWrapper<LoweringPass, FunctionPass> {
	    void runOnFunction() override {
	      // 实现lowering逻辑
	    }
	  };
	  ```
- Conversion within Dialect:
	- ```
	  struct OptimizePass : public PassWrapper<OptimizePass, FunctionPass> {
	    void runOnFunction() override {
	      // 实现优化逻辑
	    }
	  };
	  ```
- Code gen:
	- ```
	  struct CodeGenPass : public PassWrapper<CodeGenPass, FunctionPass> {
	    void runOnFunction() override {
	      // 实现代码生成逻辑
	    }
	  };
	  ```
- 集成和测试：
	- ```
	  void registerPasses() {
	    PassRegistration<OptimizePass>("optimize", "Optimize AI operations");
	    PassRegistration<LoweringPass>("lowering", "Lower AI operations to standard MLIR");
	    PassRegistration<CodeGenPass>("codegen", "Generate code from MLIR");
	  }
	  ```
	- ```
	  // 测试用例
	  module {
	    func @test(%arg0: tensor<10xf32>) -> tensor<10xf32> {
	      %0 = "myai.op"(%arg0) : (tensor<10xf32>) -> tensor<10xf32>
	      return %0 : tensor<10xf32>
	    }
	  }
	  ```
- Example pass:
	- ```
	  // MyPass.cpp
	  #include "mlir/IR/Builders.h"
	  #include "mlir/IR/Function.h"
	  #include "mlir/IR/Module.h"
	  #include "mlir/Pass/Pass.h"
	  #include "mlir/Transforms/DialectConversion.h"
	  
	  using namespace mlir;
	  
	  namespace {
	  struct InsertConstantPass : public PassWrapper<InsertConstantPass, OperationPass<ModuleOp>> {
	    void runOnOperation() override {
	      // 获取当前处理的模块
	      ModuleOp module = getOperation();
	  
	      // 遍历模块中的每个函数
	      module.walk([&](FuncOp func) {
	        // 获取函数的入口块
	        Block &entryBlock = func.getBody().front();
	  
	        // 创建一个常量操作，并插入到入口块的开头
	        OpBuilder builder(&entryBlock, entryBlock.begin());
	        auto constantOp = builder.create<ConstantOp>(builder.getUnknownLoc(), builder.getI32Type(), builder.getI32IntegerAttr(42));
	  
	        // 将常量操作的结果打印出来
	        constantOp.getResult().print(llvm::outs());
	        llvm::outs() << "\n";
	      });
	    }
	  };
	  } // namespace
	  
	  // 注册Pass
	  namespace mlir {
	  void registerMyPass() {
	    PassRegistration<InsertConstantPass>(
	        "insert-constant", "Insert a constant operation at the beginning of each function");
	  }
	  } // namespace mlir
	  ```
- Another example of replacing same repeated pattern with function call:
	- ```
	  // ExtractFunctionPass.cpp
	  #include "mlir/IR/Builders.h"
	  #include "mlir/IR/Function.h"
	  #include "mlir/IR/Module.h"
	  #include "mlir/Pass/Pass.h"
	  #include "mlir/Transforms/DialectConversion.h"
	  #include "mlir/Transforms/GreedyPatternRewriteDriver.h"
	  
	  using namespace mlir;
	  
	  namespace {
	  struct ExtractFunctionPass : public PassWrapper<ExtractFunctionPass, OperationPass<ModuleOp>> {
	    void runOnOperation() override {
	      ModuleOp module = getOperation();
	      MLIRContext *context = module.getContext();
	  
	      // 定义用于匹配的模式
	      RewritePatternSet patterns(context);
	      patterns.add<PatternToFunc>(context);
	  
	      // 应用模式到模块
	      if (failed(applyPatternsAndFoldGreedily(module, std::move(patterns)))) {
	        signalPassFailure();
	      }
	    }
	  };
	  
	  struct PatternToFunc : public OpRewritePattern<AddIOp> {
	    using OpRewritePattern<AddIOp>::OpRewritePattern;
	  
	    LogicalResult matchAndRewrite(AddIOp addOp, PatternRewriter &rewriter) const override {
	      auto func = addOp->getParentOfType<FuncOp>();
	      if (!func) return failure();
	  
	      // 检查模式是否匹配
	      if (!matchPattern(addOp.getResult(), m_Val(), m_Val())) return failure();
	      auto nextOp = dyn_cast_or_null<MulIOp>(addOp.getResult().use_begin()->getOwner());
	      if (!nextOp || nextOp.getOperand(0) != addOp.getResult()) return failure();
	  
	      // 创建新的函数
	      auto module = func->getParentOfType<ModuleOp>();
	      OpBuilder moduleBuilder(module.getBodyRegion());
	      auto funcType = rewriter.getFunctionType(addOp.getOperands(), nextOp.getResult().getType());
	      auto newFunc = moduleBuilder.create<FuncOp>(rewriter.getUnknownLoc(), "extracted_func", funcType);
	      newFunc.addEntryBlock();
	  
	      // 将模式中的操作移动到新函数
	      Block *entryBlock = newFunc.getBody().front();
	      rewriter.setInsertionPointToStart(entryBlock);
	      auto newAdd = rewriter.create<AddIOp>(rewriter.getUnknownLoc(), entryBlock->getArgument(0), entryBlock->getArgument(1));
	      auto newMul = rewriter.create<MulIOp>(rewriter.getUnknownLoc(), newAdd.getResult(), entryBlock->getArgument(2));
	      rewriter.create<ReturnOp>(rewriter.getUnknownLoc(), newMul.getResult());
	  
	      // 替换原始操作模式
	      rewriter.setInsertionPoint(addOp);
	      auto callOp = rewriter.create<CallOp>(addOp.getLoc(), newFunc, addOp.getOperands());
	      rewriter.replaceOp(nextOp, callOp.getResults());
	      rewriter.eraseOp(addOp);
	  
	      return success();
	    }
	  };
	  } // namespace
	  
	  // 注册Pass
	  namespace mlir {
	  void registerExtractFunctionPass() {
	    PassRegistration<ExtractFunctionPass>(
	        "extract-function", "Extract repeated patterns into a function");
	  }
	  } // namespace mlir
	  ```
-