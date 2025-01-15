- #se-pointer
- [[SE-pointer for nearest upsampling]]
- From Compiler end, need to configure the IDU se-pointer following the code:
- For example:
	- ```
	  For a 3x3x1 input, upsample 2x:
	  [[0 0 1 1 2 2]
	   [0 0 1 1 2 2]
	   [3 3 4 4 5 5]
	   [3 3 4 4 5 5]
	   [6 6 7 7 8 8]
	   [6 6 7 7 8 8]]
	   For 3x3x2 input, upsample 2x:
	   [[ 0  0  2  2  4  4]
	    [ 0  0  2  2  4  4]
	    [ 6  6  8  8 10 10]
	    [ 6  6  8  8 10 10]
	    [12 12 14 14 16 16]
	    [12 12 14 14 16 16]]
	  ```
- ```python
  import numpy as np
  old_width = 3
  old_height = 3
  inputChannels = 1
  new_width = 6
  new_height = 6
  scale_factor_width = int(new_width/old_width)
  scale_factor_height = int(new_height/old_height)
  channelIncrement = inputChannels
  activationseptr = [0] * 36
  for row in range(0, int(new_height / scale_factor_height)):
      for col in range(0, int(new_width / scale_factor_width)):
          old_offset = (row*old_width + col) * channelIncrement
          for new_row in range(0, scale_factor_height):
              for new_col in range(0, scale_factor_width):
                  new_offset = row*new_width*scale_factor_height + new_row*new_width + col*scale_factor_width + new_col
                  activationseptr[new_offset] = old_offset
  print(activationseptr)
  print(np.reshape(activationseptr, [new_width, new_height]))
  ```
- Dilated convolution:
	-