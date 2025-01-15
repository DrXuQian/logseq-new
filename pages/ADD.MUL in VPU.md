title:: ADD/MUL in VPU
- alias:: ELTWISE in VPU
- If happen in DPU:
	- In VPU4.0, 64 PPE per DPU
		- 256 inputs per cycle, 64 outputs per cycle (INT8, 32 for fp16)
		- So, this is output bound, the cycle is input feature size / (64 INT8, 32 fp16)
- If happen in SHAVE:
	- 32 fp16 outputs per SHAVE, 2 shave per DPU
	- in total, 64 fp16 outputs per DPU, need to read the two inputs and write the one output, so it takes 3x (why not 4x, compute time not considered?)