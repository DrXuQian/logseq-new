- ```
  module {
    func @example(%arg0: i32) -> i32 {
      %0 = constant 1 : i32
      %1 = addi %arg0, %0 : i32
      return %1 : i32
    }
  }
  ```
- module中包含函数，全局变量和其他全局资源：
- ```
  // -----// IR Dump After OneShotBufferizeVPU2VPUIP (one-shot-bufferize-VPU-to-VPUIP) //----- //
  #NCHW = affine_map<(d0, d1, d2, d3) -> (d0, d1, d2, d3)>
  #NHWC = affine_map<(d0, d1, d2, d3) -> (d0, d2, d3, d1)>
  #NWCH = affine_map<(d0, d1, d2, d3) -> (d0, d3, d1, d2)>
  module @convolution_model attributes {VPU.arch = #VPU.arch_kind<NPU40XX>, VPU.compilationMode = #VPU.compilation_mode<DefaultHW>, VPU.revisionID = #VPU.revision_id<REVISION_B>} {
    IE.PipelineOptions @Options {
      IE.Option @VPU.ReduceSupported : false
      IE.Option @VPU.AutoPaddingODU : false
      IE.Option @VPU.BarrierMaxVariantSum : 64
      IE.Option @VPU.BarrierMaxVariantCount : 128
    }
    IE.TileResource 4 of @NCE at 1.850000e+03 MHz {
      IE.MemoryResource 1327104 bytes of @CMX_NN_FragmentationAware
      IE.MemoryResource 1474560 bytes of @CMX_NN {VPU.bandwidth = 64 : i64, VPU.derateFactor = 1.000000e+00 : f64}
      IE.ExecutorResource 2 of @SHAVE_ACT
      IE.ExecutorResource 1 of @DPU
    }
    IE.ExecutorResource 1 of @M2I
    IE.ExecutorResource 2 of @DMA_NN
    IE.MemoryResource 4194304000 bytes of @DDR {VPU.bandwidth = 64 : i64, VPU.derateFactor = 6.000000e-01 : f64}
    IE.CNNNetwork entryPoint : @main inputsInfo : {
      DataInfo "input" : tensor<1x32x256x256xf16>
    } outputsInfo : {
      DataInfo "Convolution_5" : tensor<1x32x256x256xf16>
    }
    func.func @main(%arg0: memref<1x32x256x256xf16>) -> memref<1x32x256x256xf16> {
    ...
  ```
-
-