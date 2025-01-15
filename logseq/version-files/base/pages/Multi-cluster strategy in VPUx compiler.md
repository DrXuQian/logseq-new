- https://github.com/intel-sandbox/FlexTechSharing/blob/master/AI_software_stack/vpux_compiler_sharing_session/main_list.md
- https://videoportal.intel.com/media/VPU+related+sharing+session-20220826_130220-Meeting+Recording/0_samzezum
- DONE Discussion with Yao
  :LOGBOOK:
  CLOCK: [2022-08-29 Mon 09:09:56]--[2023-02-02 Thu 20:04:02] =>  3778:54:06
  :END:
- #tiling
- SOK:
	- ![image.png](../assets/image_1661661393458_0.png)
- HKSwitch
	- ![image.png](../assets/image_1661661464030_0.png)
- Eltwise SOK is able to run but low efficient
	- I don't totally agree...
	- SOK convolution -> SOK elmentwise
		- No need for broadcast
	- ![image.png](../assets/image_1661661606173_0.png)