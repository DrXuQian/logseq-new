---
title: genetic alogrithms
---
- Recall questions
	- What is genetic algorithm?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:10:37.960Z
	  card-last-reviewed:: 2023-01-28T15:10:37.961Z
	  card-last-score:: 5
		- ((28528f8e-f7a8-4135-983b-8747c08a1e71))
	- What are the five phases in genetic algorithms?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:02.987Z
	  card-last-reviewed:: 2023-01-28T15:14:02.988Z
	  card-last-score:: 5
		- ((9a5e4fc6-7461-4e0f-af94-e960d10fd49e))
	- Why we need mutation in genetic algorithms?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:21.269Z
	  card-last-reviewed:: 2023-01-28T15:12:21.270Z
	  card-last-score:: 5
		- To prevent premature and create random search for possible solutions.
	- What does population mean in genetic algorithms?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:59.283Z
	  card-last-reviewed:: 2023-01-28T15:13:59.284Z
	  card-last-score:: 5
		- population is the set of possible solutions for the optimization problems.
	- What is chromosome in genetic algorithms?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:00.499Z
	  card-last-reviewed:: 2023-01-28T15:14:00.500Z
	  card-last-score:: 5
		- a possible solution for the optimization problem.
	- What does gene mean in genetic algorithms?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:35.731Z
	  card-last-reviewed:: 2023-01-28T15:13:35.731Z
	  card-last-score:: 5
		- gene is one of the variables of a option in a chromosome.
		- ((c91c4aa0-8f8b-44af-b6ff-ecefe32b62b8))
- In general, genetic algorithms do the following
  id:: 28528f8e-f7a8-4135-983b-8747c08a1e71
	- 1. randomly generate a bunch of populations with random distributions and evaluate these populations (populations encoded in 0 and 1s)
	- 2. generate new generations based on the previous results.
		- select parents from the previous generations, divide and combine parents to form children.
		- do mutation on each item randomly
		- keep the best of the previous generation each time
	- 3. keep the upper process until we get the desired output or iteration reach limit
- In detail, the genetic algorithm contains the following 5 phases
  id:: 9a5e4fc6-7461-4e0f-af94-e960d10fd49e
	- Initial population
		- ![](../assets/ttkkhGU3iR.png)
		  id:: c91c4aa0-8f8b-44af-b6ff-ecefe32b62b8
	- Fitness function
		- The **fitness function** determines how fit an individual is (the ability of an individual to compete with other individuals). It gives a **fitness score** to each individual. The probability that an individual will be selected for reproduction is based on its fitness score.
	- Selection
		- Two pairs of individuals (**parents**) are selected based on their fitness scores. Individuals with high fitness have more chance to be selected for reproduction.
	- Crossover
		- ![](../assets/BcCPVDfTvX.png)
			- Crossover point
		- ![](../assets/RWlyk0pqh8.png)
			- Exchange genes until meet crossover point
		- ![](../assets/H4QroN-U_Z.png)
			- New offspring
	- Mutation
		- Mutation occurs to maintain diversity within the population and prevent premature convergence.
		- ![](../assets/opze2oiAGn.png)
	- Termination
		- The algorithm terminates if the population has converged (does not produce offspring which are significantly different from the previous generation)
- Resources
	- https://towardsdatascience.com/introduction-to-genetic-algorithms-including-example-code-e396e98d8bf3
	- {{youtube https //www.youtube.com/watch?v=uQj5UNhCPuo}}
	- {{youtube https //www.youtube.com/watch?v=nhT56blfRpE}}
	- [kiecodes/genetic-algorithms: This repository belongs to the youtube videos "What are Genetic Algorithms" (https://youtu.be/uQj5UNhCPuo) and "Genetic Algorithm from Scratch in Python" (https://youtu.be/nhT56blfRpE). If you haven't seen it, please consider watching part one of this series, to get a better understanding of this code.](https://github.com/kiecodes/genetic-algorithms)
	- [Genetic Algorithm in Python generates Music (code included) - YouTube](https://www.youtube.com/watch?v=aOsET8KapQQ)