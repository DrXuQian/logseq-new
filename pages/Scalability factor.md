---
title: Scalability factor
---
- Recall Questions:
	- What is linear scalability?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:03.766Z
	  card-last-reviewed:: 2023-01-28T15:14:03.766Z
	  card-last-score:: 5
		- > When you scale up your system's hardware capacity, you want the workload it is able to handle to scale up to the same degree. If you double hardware capacity of your system, you would like your system to be able to handle double the workload too. This situation is called "linear scalability".
	- What is the scalability factor for AI hardware accelerators?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:27.722Z
	  card-last-reviewed:: 2023-01-28T15:11:27.722Z
	  card-last-score:: 5
		- increase dpu_freq, speed up compute but not dma because of the limitation of ddr_bw
	- What is vetical and horizontal scalability?
		- Vertical: scale up the system you are deploying the task with higher capacity.
			- for example, faster CPU, more memory. faster and larger hard disk, faster memory bus etc
		- Horizontal: scale up the system by adding more hardware you are deploying the task on
			- for example, more computers for the program
	-