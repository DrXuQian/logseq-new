- #midasnet
- [[Midasnet]]
- ## Contexts:
	- [[May 4th, 2022]]
	  collapsed:: true
		- [5/4/2022 6:43 AM] Crews, Darren S
		  > Acer is would like to use this model called Midasnet which is for monocular depth estimation.  Right now the GOPs are kind of high so it might be hard to run on the VPU at the required frame rate.  But it might be interesting for Dorrepaal, Ronan/Qian, Xu to do a NAS based version that could give the same accuracy but higher performance.  Roy, Shivaji feel free to comment on the current performance estimates.  Here's the link to the model: https://docs.openvino.ai/latest/omz_models_model_midasnet.html
		- [5/4/2022 6:45 AM] Crews, Darren S
		  > Acer would like to run this model alongside the MEP use case, so we will have a limited amount of compute to dedicate to Midasnet
		- [5/4/2022 6:49 AM] Roy, Shivaji
		  > For sure we need to optimize it. In its current compute it won't run on MTL along side MEP. We do have a smaller version of this network however we don't know if they have similar accuracy or not.
		- [5/4/2022 8:53 AM] Qian, Xu
		  > I can cooperate with Ronan and look into this to see if we can improve this network using NAS. Do we have any projection on Midasnet like spreadsheet analysis? Also, this is for MTL, right?
		- [5/4/2022 10:27 AM] Crews, Darren S
		  > Roy, Shivaji should be able to share the analysis.  This would be targeted for MTL, but I believe the ask was originally for KMB.  But I would just focus on MTL for now
		- [5/4/2022 1:00 PM] Roy, Shivaji
		  > we have the original midasnet (midasnet_int8.xml) and then we have a optimized version from CCE (midas_v21_small.xml). I am not sure if the smaller version is even a representative of original. I have the projection for both on KMB.
		  [midas_v21_small.bin]
		  (https://intel-my.sharepoint.com/personal/shivaji_roy_intel_com/Documents/midasnet/midas_v21_small.bin)
		  [midas_v21_small.xml]
		  (https://intel-my.sharepoint.com/personal/shivaji_roy_intel_com/Documents/midasnet/midas_v21_small.xml)
		  [midasnet_int8.bin]
		  (https://intel-my.sharepoint.com/personal/shivaji_roy_intel_com/Documents/midasnet/midasnet_int8.bin)
		  [midasnet_int8.xml]
		  (https://intel-my.sharepoint.com/personal/shivaji_roy_intel_com/Documents/midasnet/midasnet_int8.xml)
		- [5/4/2022 1:05 PM] Roy, Shivaji
		  > [midasnet_kpi.xlsx]
		  (https://intel-my.sharepoint.com/personal/shivaji_roy_intel_com/Documents/midasnet/midasnet_kpi.xlsx)
		- > Yes, The interpolations are bi-linear and currently the cost is with DPU. The ccg team also proposed some improvement to interpolation by using Mode=asymmeteric instead of Align corners. To improve the perf further. The spreadsheet don't have that cost.
		- ![image.png](../assets/image_1652073047327_0.png)
	- [[May 11th, 2022]]
	  collapsed:: true
		- [Yesterday 10:52 PM] Qian, Xu
		  Crews, Darren S, I benchmarked the fps of Midas_v21_small with 512x288 input resolution on KMB based on Roy's spreadsheet. The fps is 126.77 on KMB. This is 2x higher than the requirement (60 fps for 512x288 video) from Acer. But a real test on KMB would be better demonstration of the performance. Also, two contributor of the Midas repo used to work for Intel Lab, but none of them are currently working for Intel. Me and Ronan will try to find all the 10 dataset and reproduce Midas, but in the meantime, we will start retraining Midas with one of the datasets. Does this sound good?
		- [Yesterday 10:53 PM] Crews, Darren S
		  sounds good, thanks.  We should look at the schedule simulator of midas plus MEP.  We are barely meeting MEP on KMB, so the smaller we can make midas the more likely we can run midas and MPE at the same time
		- [Yesterday 11:32 PM] Roy, Shivaji
		  We already have measured data from CCG. This was measured on KMB and the blob generated with MCM compiler. Running the network @30 seems to meeting the MEP targets. I expect improvement to MEP models since these measurements. Also if we can improve midasnet further we can hopefully achieve the goal easily.
		- [Yesterday 11:47 PM] Roy, Shivaji
		  Actually we are too far off from FPS perspective. I think we need to measure it one more time.
		- [7:59 AM] Roy, Shivaji
		  Yes, I think the currently they could only reach 60FPS.
		- [8:00 AM] Qian, Xu
		  That is way off our profiling results.
		- ![image.png](../assets/image_1652226889483_0.png)
- Meeting with CuiJing
	- 1.	Gaming + AI laptop/ Education /Consumer laptop use-cases
	- 2.	All use-cases will run MEP + MIDAS
	- 3.	Latency requirements for the Depth Map
		- a.	Video: 60 fps with 16 ms latency
		- b.	Image: can use lower fps but don’t have any requirement ask per-se
		- c.	Jing & team using MCM compiler
		- d.	Resolution:
			- i.	Video: 512 x 288
			- ii.	Image: 672 x 384
	- 4.	midas_512x288.blob for VPU  (Jing/Chance) / midas_v21_small.xml for CPU  (Glen) = Same network
	- 5.	midas_384x384  (with VPU team not enabled yet) – same network but higher resolution
	- 6.	Acer will use full MEP feature
	- 7.	MiDas model not from Acer but from OVMZ
		- a.	250 fps on KMB projection
		- b.	700 fps on MTL projection
	- 8.	We have confidence that MEP as currently defined + MIDAS can work on MTL
	- 9.	For KMB, we will need to tune the MiDAS model to optimize for VPU. AR: VPU team
	- ![image.png](../assets/image_1652178135704_0.png)
	-
- ## NAS candidate block analysis
	- `python nbperf --input /home/qianxu/Desktop/ai.vpu.arch.nas.models/SingleImageSuperResolution/midas_v21_small.xml  --device vpu2p0 --backend vpunn --output midas.svg`
		- 99 fps from vpunn
	- `python nbperf --input /home/qianxu/Desktop/ai.vpu.arch.nas.models/SingleImageSuperResolution/midas_v21_small_dw_pw.xml  --device vpu2p0 --backend vpunn --output midas.svg`
		- 120 fps from vpunn