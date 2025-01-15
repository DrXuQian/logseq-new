- #NTHW_NTK
- Configuration:
	- https://github.com/intel-innersource/frameworks.vpu.models.vpu-em/blob/4ebbefa47370f9ba73f76715da305ce8438bf7a6/vpu_em/engine/engineVPU.py#L42
	- ```python
	  @dataclass
	  class LSCLVariantTableVPU5:
	      """
	          order: nc ntc nk nh nw nth ntw nthw ntk nt
	              context per MPE: nt = nthw * ntk = nth * ntw * ntk = 32
	              1 activation context: nh * nw = 4 x 4, nc * ntc = 64, with nc determined by stencil
	              1 weight context: nk = 16, nc * ntc = 64 with nc determined by stencil
	      """
	      entries : dict = field(default_factory=lambda: {
	          #LSCLVariantEntry: nc ntc  nk  nh  nw  nth ntw nthw ntk nt
	          # For VPU5 4K config
	          # the follow three entries are old 64-context entries no longer supported by VPU5
	          # but left in VPU-EM for modeling purpose
	          # "8_8": LSCLVariantEntry(16, 8, 16, 4, 4, 2, 4, 8, 8, 64),
	          # "4_16": LSCLVariantEntry(16, 8, 16, 4, 4, 2, 2, 4, 16, 64),
	          # "16_4": LSCLVariantEntry(16, 8 , 16, 4, 4, 4, 4, 16, 4, 64),
	          #LSCLVariantEntry:       nc ntc nk nh nw nth ntw nthw ntk nt
	          "8_8":  LSCLVariantEntry(16, 4, 16, 4, 4, 2,  4,   8,  4, 32),
	          "4_16": LSCLVariantEntry(16, 4, 16, 4, 4, 2,  2,   4,  8, 32),
	          "16_4": LSCLVariantEntry(16, 4, 16, 4, 4, 4,  4,  16,  2, 32),
	  
	          # the folloiwng table entries are new to VPU5 for 32-context and more stencil configs
	          # in addition to 4x4, we have 4x1, 8x1, 8x2 and 16x1
	          #LSCLVariantEntry:             nc ntc nk  nh  nw nth ntw nthw ntk nt
	          "8_4_HW4x4":  LSCLVariantEntry(16, 4, 16, 4,   4, 2,  4,  8,   4, 32),
	          "4_8_HW4x4":  LSCLVariantEntry(16, 4, 16, 4,   4, 2,  2,  4,   8, 32),
	          "16_2_HW4x4": LSCLVariantEntry(16, 4, 16, 4,   4, 4,  4, 16,   2, 32),
	          #LSCLVariantEntry:             nc ntc nk  nh  nw nth ntw nthw ntk nt
	          "8_4_HW4x1":  LSCLVariantEntry(64, 1, 16, 4,   1, 2,  4,  8,   4, 32),
	          "4_8_HW4x1":  LSCLVariantEntry(64, 1, 16, 4,   1, 2,  2,  4,   8, 32),
	          "16_2_HW4x1": LSCLVariantEntry(64, 1, 16, 4,   1, 4,  4, 16,   2, 32),
	          #LSCLVariantEntry:             nc ntc nk  nh  nw nth ntw nthw ntk nt
	          "8_4_HW8x1":  LSCLVariantEntry(32, 2, 16, 8,   1, 2,  4,  8,   4, 32),
	          "4_8_HW8x1":  LSCLVariantEntry(32, 2, 16, 8,   1, 2,  2,  4,   8, 32),
	          "16_2_HW8x1": LSCLVariantEntry(32, 2, 16, 8,   1, 4,  4, 16,   2, 32),
	          #LSCLVariantEntry:             nc ntc nk  nh  nw nth ntw nthw ntk nt
	          "8_4_HW8x2":  LSCLVariantEntry(16, 4, 16, 8,   2, 2,  4,  8,   4, 32),
	          "4_8_HW8x2":  LSCLVariantEntry(16, 4, 16, 8,   2, 2,  2,  4,   8, 32),
	          "16_2_HW8x2": LSCLVariantEntry(16, 4, 16, 8,   2, 4,  4, 16,   2, 32),
	          #LSCLVariantEntry:             nc ntc nk  nh  nw nth ntw nthw ntk nt
	          "8_4_HW16x1": LSCLVariantEntry(16, 4, 16, 16,  1, 2,  4,   8,  4, 32),
	          "4_8_HW16x1": LSCLVariantEntry(16, 4, 16, 16,  1, 2,  2,   4,  8, 32),
	          "16_2_HW16x1":LSCLVariantEntry(16, 4, 16, 16,  1, 4,  4,  16,  2, 32)
	      })
	  ```
	-
- Testcase
	- https://github.com/intel-innersource/frameworks.vpu.models.vpu-em/blob/4ebbefa47370f9ba73f76715da305ce8438bf7a6/tests/misc/test_dpu_v5_conv_stencil.py
- [[Accumulator Context]]
- [[NTHW_NTK]]