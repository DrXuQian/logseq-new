- [[ODU transpose]]
  [[ODU stride]]
- #ODU
- firmware handling of DPU:
	- https://github.com/intel-innersource/firmware.vpu.client/blob/89f958d12edb7daa01862357097822019728fd37/vpu2/system/nn/nce_lib/src/3720/nn_nce_lib.cpp
- Set up output in firmware:
	- https://github.com/intel-innersource/firmware.vpu.client/blob/89f958d12edb7daa01862357097822019728fd37/vpu2/system/nn/nce_lib/src/3720/nn_nce_lib.cpp#LL441C21-L441C21
- Task splition
	- ```
	              // When permuting dimensions, Splitting over H or K will rotate the output dimentions based on the
	              // permutation, which is unclear how the split inferences merge to a common output tensor. We should be able
	              // to support some versions os SOH with some permutations, but we cannot guarantee all-to-all combinations
	              // of all permutations to all splits with a simple formula. For this reason for the time being we remove all
	              // SOK/SOH support when permuting the output, and we will enable usecase by usecase, if such feature is
	              // needed in the future.
	  ```
- For SOK:
	- ```
	                "output_data": {
	                  "name": "temp-478",
	                  "dimensions": [
	                    1,
	                    16,
	                    1,
	                    64
	                  ],
	                  "strides": [
	                    2.0,
	                    4096.0,
	                    2.0,
	                    4096.0,
	                    64.0
	                  ],
	  ```
- ODU document:
	- [NCE_DPU_ODU.docx](https://intel-my.sharepoint.com/:w:/p/xu_qian/EXNwN6mAGX9Go0tfqJoAoHQBT3B7I9wBiJjdjc73L1XavQ?e=rHpezl)