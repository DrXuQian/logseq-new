- Image processing
	- the inp_images is with four channels, last channel being depth channel.
	- The first three channels from OpenEXR, HDR image, according to [XeSS deep dive_2022-07-22.pptx](https://intel.sharepoint.com/:p:/r/sites/axggraphicsresearchorganization/Shared%20Documents/Neural%20Graphics%20Program%20Updates/XeSS/XeSS%20deep%20dive_2022-07-22.pptx?d=w60960f27847c459a88c2cbb91a5be65f&csf=1&web=1&e=gGZ2VL)
		- So first layer and last layer cannot quantize
	- Warp the vel with previous output.
		- Input + jitter (zeros?)
		-
	- ```
	  
	  out_prev = self.warp(out, vel)
	  inp = torch.cat((inp_images[:, :3, idx, ...], zeros), dim=1)
	  ref = ref_images[:, :3, idx, ...]
	  
	  ```