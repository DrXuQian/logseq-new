- Nbperf related posts:
	- [[Temporal tiling + spatial tiling]]
- #vpux
- 判断能否cmx concat：
	- 需要做 isPotentialCMXConcat， potentialInputPattern inputPatternCanBeCMXed potentialOutputPattern outputPatternCanBeCMXed areInputOutputPatternsCompatible
	- ![image.png](../assets/image_1667368491377_0.png)
- src/vpux_compiler/src/dialect/VPU/passes/cmx_concat.cpp:992
- src/vpux_compiler/src/dialect/VPU/passes/cmx_concat.cpp:925
- `IsPotentialCMXConcat`:
	- must be single axis
	- ```
	      // Check if the Concat op satisfies the CMX Concat conditions or not
	      auto isSingleAxisConcat = [](mlir::ArrayAttr offset) {
	          // If a concat has at least one static_offset attribute of 2 or more non-zero axis
	          // it is considered as multiple-axis concat, vice versa
	          // e.g., static_offset of a multiple-axis concat:
	          // [0, 0, 0, 0], [0, 0, 0, 1], [0, 0, 1, 0], [0, 0, 1, 1]
	          auto offsetVector = parseIntArrayAttr<int64_t>(offset);
	          return offsetVector.size() - std::count(offsetVector.begin(), offsetVector.end(), 0) <= 1;
	      };
	  ```
- Input rewrite pattern:
	- ![image.png](../assets/image_1667442752747_0.png){:height 502, :width 693}
- Output rewrite pattern:
	- ![image.png](../assets/image_1667442800780_0.png){:height 571, :width 697}
- Why intel-dns-lstm failed to concat in CMX:
	- log:
		- ```
		  09:32:02.924 [cmx-concat]  Run on Function 'main'
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_3")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_7")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_11")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_15")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_19")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_23")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_75")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_87")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_99")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_111")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_123")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  09:32:02.924 [cmx-concat]    Got 'VPU.Concat' at 'loc("Concat_135")'
		  09:32:02.924 [cmx-concat]        InputPattern mismatch: Copy op is not found
		  09:32:02.924 [cmx-concat]      Concat input pattern not valid
		  ```