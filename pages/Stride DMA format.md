- VPUX compiler output:
	- ```json
	  "task_type": "NNDMATask",
	            "task": {
	              "src": {
	                "name": "temp-1",
	                "dimensions": [
	                  1,
	                  128,
	                  2,
	                  32
	                ],
	                "strides": [
	                  4.0,
	                  32768.0,
	                  256.0,
	                  128.0,
	                  4.0
	                ],
	                "data": {
	                  "data_index": 0
	                },
	                "locale": "ProgrammableInput",
	                "locale_index": [
	                  2
	                ],
	                "data_dtype": "FP32",
	                "quant_zero": [
	                  0
	                ],
	                "quant_mult": [
	                  1
	                ],
	                "quant_shift": [
	                  0
	                ],
	                "order": 4660,
	                "base_ptrs": [
	  
	                ]
	              },
	              "dst": {
	                "name": "temp-511",
	                "dimensions": [
	                  1,
	                  128,
	                  2,
	                  32
	                ],
	                "strides": [
	                  4.0,
	                  32768.0,
	                  256.0,
	                  128.0,
	                  4.0
	                ],
	                "data": {
	                  "data_index": 125184
	                },
	                "locale": "VPU_CMX_NN",
	                "locale_index": [
	                  0
	                ],
	                "data_dtype": "FP32",
	                "quant_zero": [
	                  0
	                ],
	                "quant_mult": [
	                  1
	                ],
	                "quant_shift": [
	                  0
	                ],
	                "order": 4660,
	                "base_ptrs": [
	  
	                ]
	              },
	              "set_ord": true
	            }
	  
	  ```
- NBperf:
	- ```json
	  {
	                      "name": "DMA",
	                      "id": 415000,
	                      "dma_descriptor": [
	                          {
	                              "HWP_ID_EN": true,
	                              "HWP_ID": 0,
	                              "NUM_DIM": 4,
	                              "SRC_DIM_SZ": [
	                                  64,
	                                  1,
	                                  1,
	                                  528
	                              ],
	                              "DST_DIM_SZ": [
	                                  64,
	                                  1,
	                                  1,
	                                  528
	                              ],
	                              "SRC_STRIDE": [
	                                  64 * 528,
	                                  528,
	                                  528,
	                                  1
	                              ],
	                              "DST_STRIDE": [
	                                  64,
	                                  1,
	                                  1,
	                                  528
	                              ],
	                              "SRC_WIDTH": 33792,
	                              "DST_WIDTH": 33792,
	                              "SRC_ADDR": 2149301248,
	                              "DST_ADDR": 740015104,
	                              "SRA": false,
	                              "DRA": false,
	                              "SBA_ADDR": 0,
	                              "DBA_ADDR": 0,
	                              "BARR_EN": false,
	                              "BARR_PMASK": [],
	                              "BARR_CMASK": [],
	                              "SBL": 512,
	                              "DBL": 512,
	                              "ARB_QOS": 4,
	                              "src_locale": "DDR",
	                              "src_locale_index": [
	                                  0
	                              ],
	                              "dst_locale": "CMX",
	                              "dst_locale_index": [
	                                  0
	                              ]
	                          }
	                      ],
	                      "params": {
	                          "size": 65536,
	                          "direction": "DDR2CMX",
	                          "type": "WEIGHT",
	                          "sparsity": 0.0
	                      },
	                      "ctx_id": -1,
	                      "consumer_taskid": 221000
	                  },
	  ```