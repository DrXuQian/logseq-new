title:: vpuxcompiler: reshape
- ```c++
      const auto outOrder = DimsOrder::fromValue(origOp.output());
      const auto outShape = getShape(origOp.output());
      const auto outShapeAttr = getIntArrayAttr(getContext(), outShape);
  
      // replace reshape with new reshape
      auto newReshape = rewriter.replaceOpWithNewOp<IE::ReshapeOp>(origOp, first_mempermute.input(), nullptr, false, outShapeAttr);
      layoutinfo = vpux::IE::getLayoutInfo(newReshape);
  ```
- #如何构建ReshapeOp
-
- `reshape` 的限制
	- [输出必须是`NCHW`](https://github.com/intel-innersource/applications.ai.vpu-accelerators.vpux-plugin/pull/383/files/d341f7ce73e36777e5cbb7ab96e1dd3e075cfbde#diff-279700627bbb1fc7ca15981d557707c2e3fc8a317b98ed7f0ec42f34597004af)
- 可以用`affineReshape`来保证输入输出的layout一致性