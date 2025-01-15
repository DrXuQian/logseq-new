- [[VPUx compiler feasible memory]]
- ```c++
  
      // 3. optimize spills
      FeasibleMemorySchedulerSpilling spilling(netFunc, _memSpace, _secondLvlMemSpace, depsInfo, aliasesInfo, _log, scan);
      spilling.optimizeDataOpsSpills(scheduledOps);
      spilling.removeRedundantSpillWrites(scheduledOps);
  
      // 3. re-order the IR
      updateAsyncExecuteOpPosition(netFunc, depsInfo, scheduledOps);
  
      // 4. insert spill dmas
      spilling.insertSpillCopyOps(scheduledOps);
  
      // 5. update dependencies
      // Recreate aliasesInfo after spill insertion to get updated information about
      // root buffers of affected spill result users.
      aliasesInfo = AliasesInfo{netFunc};
      FeasibleMemorySchedulerControlEdges controlEdges(_memSpace, depsInfo, aliasesInfo, _log, scan);
      // controlEdges.insertDependenciesBasic(scheduledOps); // Old method, maintained only for debug
      controlEdges.insertMemoryControlEdges(scheduledOps);
      // Insert dependencies aligned with schedule order to properly serialize
      // operations on a given executor. This is needed for operations which might not have
      // any input data flow like dataOps (data DMAs)
      controlEdges.insertScheduleOrderDepsForExecutor(scheduledOps, VPU::ExecutorKind::DMA_NN);
  
      controlEdges.updateDependenciesInIR();
  
      // 6. convert to allocated ops
      mlir::ConversionTarget target(ctx);
      target.addLegalDialect<IERT::IERTDialect>();
      target.addLegalDialect<VPURT::VPURTDialect>();
      target.addDynamicallyLegalOp<mlir::memref::AllocOp>([&](mlir::memref::AllocOp op) {
          const auto type = op.memref().getType().cast<vpux::NDTypeInterface>();
          return type == nullptr || type.getMemSpace() != _memSpace;
      });
      target.addDynamicallyLegalOp<VPURT::AllocDistributed>([&](VPURT::AllocDistributed op) {
          const auto type = op.buffer().getType().cast<vpux::NDTypeInterface>();
          return type == nullptr || type.getMemSpace() != _memSpace;
      });
  
      mlir::RewritePatternSet patterns(&ctx);
      patterns.add<AllocRewrite>(scan.handler(), &ctx, _log);
      patterns.add<AllocDistributedRewrite>(scan.handler(), &ctx, _log);
  
      if (mlir::failed(mlir::applyPartialConversion(module, target, std::move(patterns)))) {
          _log.error("Failed to replace Alloc/Dealloc Operations");
          signalPassFailure();
          return;
      }
  
      IE::setUsedMemory(module, _memSpace.getFullReference(), scan.handler().maxAllocatedSize());
  }
  ```
-
- `safeRunOnModule`
- `getContext`
	- What is the Context in MLIR? #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:07.703Z
	  card-last-reviewed:: 2023-01-28T15:14:07.703Z
	  card-last-score:: 5
		- Return the context this operation is associated with.
		- Definition at line 99 of file `Operation.h`.
- `getOperation`
	- What is the Operation in MLIR? #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:56.490Z
	  card-last-reviewed:: 2023-01-28T15:13:56.490Z
	  card-last-score:: 5
		-
- `class FeasibleMemoryScheduler`
	- [[C++ final specifier]]
		- What is the final specifier in C++? #card
		  collapsed:: true
		  card-last-interval:: 4
		  card-repeats:: 2
		  card-ease-factor:: 2.46
		  card-next-schedule:: 2023-02-01T15:14:14.291Z
		  card-last-reviewed:: 2023-01-28T15:14:14.291Z
		  card-last-score:: 5
			- final specifier specifies that a class in the final derive from the base class
				- Specifies that a [virtual function](https://en.cppreference.com/w/cpp/language/virtual) cannot be overridden in a derived class or that a class cannot be [derived from](https://en.cppreference.com/w/cpp/language/derived_class).
				  
				      ```c++
				      struct Base
				      {
				        virtual void foo();
				      };
				  
				      struct A : Base
				      {
				        void foo() final; // Base::foo is overridden and A::foo is the final override
				        void bar() final; // Error: bar cannot be final as it is non-virtual
				      };
				  
				      struct B final : A // struct B is final
				      {
				        void foo() override; // Error: foo cannot be overridden as it is final in A
				      };
				  
				      struct C : B // Error: B is final
				      {
				      };
				  ```
-