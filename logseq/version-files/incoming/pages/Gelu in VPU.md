- Formula Gelu:
	- ![image.png](../assets/image_1650588395677_0.png){:height 66, :width 511}
	- ![image.png](../assets/image_1650588407817_0.png)
- Assembly
	- unrolled assembly code, 6 times
	- ```
	  LSU0.LDO.64 v13.1 i5 8 || LSU1.LD.64 v13.0 i5
	  NOP 6
	  VAU.MUL.f16 v8 v13 v13 // V^2
	  NOP 2
	  VAU.MUL.f16 v8 v13 v8 // V^3
	  NOP
	  CMU.CP.128.16.r v15 i4.0 // copy 0.0447 value to the register
	  VAU.MUL.f16 v8 v8 v15 // V^3 times .0447
	  NOP 2
	  VAU.ADD.f16 v13 v13 v8
	  NOP
	  LSU0.LDO.64 v14.1 i5 24 || LSU1.LDO.64 v14.0 i5 16 || CMU.CP.128.16.r v12 i7.0
	  VAU.MUL.f16 v13 v13 v12
	  NOP 2
	  VAU.TANH v8 v13
	  NOP 2
	  VAU.MUL.f16 v9 v14 v14
	  LSU0.LDO.64 v11.1 i5 -8 || LSU1.LDO.64 v11.0 i5 -16
	  VAU.ADD.f16 v8 v8 1.0
	  VAU.MUL.f16 v9 v14 v9
	  NOP
	  VAU.MUL.f16 v13 v8 v13
	  VAU.MUL.f16 v9 v9 v15
	  NOP
	  VAU.MUL.f16 v7 v11 v11
	  VAU.ADD.f16 v14 v14 v9
	  NOP
	  LSU0.LDO.64 v10.1 i5 -24 || LSU1.LDO.64 v10.0 i5 -32 || VAU.MUL.f16 v7 v11 v7
	  VAU.MUL.f16 v14 v14 v12
	  NOP
	  VAU.MUL.f16 v7 v7 v15
	  VAU.TANH v9 v14
	  NOP 2
	  VAU.MUL.f16 v6 v10 v10
	  VAU.ADD.f16 v11 v11 v7
	  VAU.ADD.f16 v9 v9 1.0
	  VAU.MUL.f16 v9 v10 v6
	  NOP
	  VAU.MUL.f16 v14 v9 v14
	  VAU.MUL.f16 v15 v9 v15
	  NOP
	  VAU.MUL.f16 v11 v11 v12
	  VAU.ADD.f16 v15 v10 v15
	  NOP
	  VAU.MUL.f16 v13 v13 0.5
	  VAU.MUL.f16 v15 v15 v12
	  VAU.TANH v7 v11
	  VAU.MUL.f16 v14 v14 0.5
	  VAU.TANH v14 v15
	  NOP 3
	  LSU1.STO.64 v14.1 i8 24 || LSU0.STO.64 v14.0 i8 16 || VAU.ADD.f16 v7 v7 1.0
	  VAU.ADD.f16 v14 v14 1.0
	  NOP
	  VAU.MUL.f16 v11 v7 v11 || IAU.ADD i9 i9 4
	  CMU.CMII.i32 i6 i9 || VAU.MUL.f16 v15 v14 v15
	  PEU.PC1C NEQ || BRU.BRA .LBB0_4
	  VAU.MUL.f16 v11 v11 0.5
	  VAU.MUL.f16 v15 v15 0.5
	  LSU1.STO.64 v13.1 i8 8 || LSU0.ST.64 v13.0 i8
	  LSU1.STO.64 v11.1 i8 -8 || LSU0.STO.64 v11.0 i8 -16
	  LSU1.STO.64 v15.1 i8 -24 || LSU0.STO.64 v15.0 i8 -32 || IAU.ADD i5 i5 64
	  IAU.ADD i8 i8 64
	  ```
- ```
  
  - # 0.5 * x * (1 + tanh(math.sqrt(2 / math.pi) * (x + 0.044715 * pow(x, 3))))
  - # sqrt( 2 / math.pi ) is a constant, ignoring calculation
  x<n:fp16>
  # pow(x,3)
  x_<n:fp16> = VAU.MUL(x<n:fp16>, x<n:fp16>)
  x_<n:fp16> = VAU.MUL(x<n:fp16>, x_<n:fp16>)
  
  x_<n:fp16> = VAU.MUL(x_<n:fp16>, 0.044715)
  x_<n:fp16> = VAU.ADD(x<n:fp16>, x_<n:fp16>)
  x_<n:fp16> = VAU.MUL(math.sqrt(2 / math.pi), x_<n:fp16>)
  
  m<n:fp16> = VAU.TANH(x_<n:fp16>)
  m<n:fp16> = VAU.ADD(m<n:fp16>, 1)
  m<n:fp16> = VAU.MUL(x<n:fp16>, m<n:fp16>)
  y<n:fp16> = VAU.MUL(0.5,m<n:fp16>)
  
  ```
- For Mobilebert, actually use Relu instead of Gelu.
	- ![12.png](../assets/12_1650588239876_0.png){:height 590, :width 444}