- operation是MLIR的基本构造单元，每个operation代表一个计算单元。可以有输入和输出。operation可以嵌套到其他的operation中。
- ```
  %result = mydialect.myop %arg0, %arg1 : (f32, f32) -> f32
  ```
- How to define an operation:
	- Define operations and types using [[TableGen]]
	- ```
	  // MyAIDialect.td
	  def MyAIDialect : Dialect {
	    let name = "myai";
	  }
	  
	  def MyMatMulOp : MyAIDialect_Ops<"matmul"> {
	    let summary = "Matrix multiplication operation";
	    let description = [{
	      This operation performs matrix multiplication on two input tensors.
	    }];
	  
	    let arguments = (ins F32Tensor:$lhs, F32Tensor:$rhs);
	    let results = (outs F32Tensor:$result);
	  
	    let assemblyFormat = [{
	      $lhs `,` $rhs `:` type($lhs)
	    }];
	  }
	  ```
- Define operator:
	- ```
	  // MyAIDialect.cpp
	  #include "mlir/IR/Builders.h"
	  #include "mlir/IR/Dialect.h"
	  #include "mlir/IR/OpImplementation.h"
	  #include "mlir/IR/PatternMatch.h"
	  #include "mlir/IR/StandardTypes.h"
	  #include "mlir/IR/TypeUtilities.h"
	  #include "mlir/Support/LogicalResult.h"
	  #include "MyAIDialect.h"
	  
	  using namespace mlir;
	  using namespace myai;
	  
	  namespace myai {
	    // 定义MyMatMulOp操作
	    class MyMatMulOp : public Op<MyMatMulOp, OpTrait::NOperands<2>::Impl, OpTrait::OneResult> {
	    public:
	      using Op::Op;
	  
	      // 获取操作的名称
	      static StringRef getOperationName() { return "myai.matmul"; }
	  
	      // 验证操作的正确性
	      static LogicalResult verify(InFlightDiagnostic &diag, MyMatMulOp op) {
	        // 验证输入类型是否为张量
	        if (!op.lhs().getType().isa<RankedTensorType>() || !op.rhs().getType().isa<RankedTensorType>())
	          return op.emitOpError("inputs must be ranked tensors");
	        return success();
	      }
	  
	      // 访问操作的输入和输出
	      Value lhs() { return getOperand(0); }
	      Value rhs() { return getOperand(1); }
	      Value result() { return getResult(); }
	  
	      // 自定义打印方法
	      static void print(OpAsmPrinter &p, MyMatMulOp op) {
	        p << "myai.matmul " << op.lhs() << ", " << op.rhs();
	      }
	  
	      // 自定义解析方法
	      static ParseResult parse(OpAsmParser &parser, OperationState &result) {
	        OpAsmParser::OperandType lhs, rhs;
	        Type type;
	        if (parser.parseOperand(lhs) || parser.parseComma() || parser.parseOperand(rhs) ||
	            parser.parseColonType(type) || parser.resolveOperand(lhs, type, result.operands) ||
	            parser.resolveOperand(rhs, type, result.operands))
	          return failure();
	        result.addTypes(type);
	        return success();
	      }
	    };
	  }
	  
	  // 注册操作到方言中
	  void MyAIDialect::initialize() {
	    addOperations<
	      MyMatMulOp
	    >();
	  }
	  ```
- MLIR的operation可以包含一个或者多个region，每个region可以包含一个或者多个block