- CUDA programming paradigm
	- heterogeneous computing
	- host which is the CPU will launch kernels on the GPU, initial mem copy, compute kernel launched by the CPU and respond to mem copy to copy data back to CPU.
	- ![image.png](../assets/image_1714356164560_0.png)
- GPU can do the following quiz #card
  ![image.png](../assets/image_1714356032282_0.png){:height 530, :width 938}
	- ![image.png](../assets/image_1714356064711_0.png){:height 463, :width 910}
- A typical GPU program
	- ![image.png](../assets/image_1714356348400_0.png){:height 386, :width 957}
- Defining the GPU computation
	- when writing a kernel, the programmer will write it as if it is a serial program and assign what ever many threads you want to launch this kernel
	- What is GPU good at? #card
	  ![image.png](../assets/image_1714356473264_0.png){:height 448, :width 950}
		- B, E
- For a same task, square calculation of the matrix element and store it in another location
	- For CPU:
		- ![image.png](../assets/image_1714357589290_0.png)
		- CPU calculation quiz #card
		  ![image.png](../assets/image_1714357643139_0.png){:height 457, :width 868}
			- ![image.png](../assets/image_1714357673851_0.png){:height 205, :width 457}
	- For GPU:
		- launch a parallel of 64 threads that with name square kernel
		- ![image.png](../assets/image_1714357748887_0.png){:height 450, :width 879}
		- Each thread knows which thread id it is running, so they can operate on different data
		- ![image.png](../assets/image_1714357850397_0.png){:height 316, :width 882}
		- GPU computation quiz #card
		  ![image.png](../assets/image_1714357909771_0.png){:height 474, :width 870}
			- latency vs throughput comparison, but this is still intra task level throughput vs latency
			- ![image.png](../assets/image_1714357931834_0.png){:height 235, :width 484}
- Fill in the blanks #card
  ![image.png](../assets/image_1714358707021_0.png){:height 466, :width 822}
	- cudaMemcpyHostToDevice
	- cudaMemcpyDeviceToHost
- Configuring the kernel launch
	- ![image.png](../assets/image_1714360837209_0.png){:height 557, :width 876}
	- 2D kernel launch
		- ![image.png](../assets/image_1714360877822_0.png){:height 531, :width 690}
	- No specification
		- ![image.png](../assets/image_1714360958282_0.png){:height 404, :width 696}
	- More generally
		- ![image.png](../assets/image_1714361006544_0.png){:height 344, :width 699}
	- What does each kernel know?
		- ![image.png](../assets/image_1714361037396_0.png){:height 263, :width 703}
	- quiz #card
	  ![image.png](../assets/image_1714361101858_0.png)
		- 8 * 4 * 2
		- 16 * 16
		- 16 * 16 * 8 * 4 * 2
- Map
	- ![image.png](../assets/image_1714372208967_0.png){:height 491, :width 907}
	- ![image.png](../assets/image_1714372250036_0.png){:height 442, :width 901}
- Parallel communication patterns
	- Gather:
		- ![image.png](../assets/image_1714380053667_0.png){:height 449, :width 719}
	- Stencil:
		- ![image.png](../assets/image_1714382049605_0.png){:height 347, :width 724}
	- Stencil:
		- ![image.png](../assets/image_1714382298119_0.png){:height 404, :width 734}