- Slice to Stride DMA
	- [1, 56,56,96] to [1, 56,56,48]
	- ```json
	  "NUM_DIM": 4,  
	  "SRC_DIM_SZ": [1,56,56,48],  
	  "DST_DIM_SZ": [1,56,56,48],  
	  "SRC_STRIDE": [301056,5376,96,1],
	  "DST_STRIDE": [150528,2688,48,1],  
	  "SRC_WIDTH": 48,  
	  "DST_WIDTH": 48,
	  ```
-
- Expand to Stride DMA
	- [1, 56,56,48] to [1, 56,56,96]
	- ```json
	  "NUM_DIM": 4,  
	  "SRC_DIM_SZ": [1,56,56,48],  
	  "DST_DIM_SZ": [1,56,56,48],  
	  "SRC_STRIDE": [150528,2688,48,1],
	  "DST_STRIDE": [301056,5376,96,1],  
	  "SRC_WIDTH": 48,  
	  "DST_WIDTH": 48,
	  ```
- DMA desc:
	- ```python
	      NUM_DIM: int = field(default=4)  #Number of dimensions enabled on descriptor. Encoded as dims-1
	      SRC_DIM_SZ: list = field(default_factory=list)  #Number of times (-1) the dimension is repeated
	      DST_DIM_SZ: list = field(default_factory=list)  #Number of times (-1) the dimension is repeated
	      SRC_STRIDE: list = field(default_factory=list)  #Byte Stride between starting addresses of dimension[N] repetitions.
	      DST_STRIDE: list = field(default_factory=list)  #Byte Stride between starting addresses of dimension[N] repetitions.
	      SRC_WIDTH: int = field(default=0)  #Byte Size of the linear part of the JOB read transfer
	      DST_WIDTH: int = field(default=0)  #Byte Size of the linear part of the JOB write transfer
	  ```
-
-