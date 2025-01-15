- {{video https://www.youtube.com/watch?v=052VkKhIaQ4&ab_channel=AbdulBari}} https://www.youtube.com/watch?v=052VkKhIaQ4&ab_channel=AbdulBari
	- m coloring optimization problem
	- m coloring decision problem
	- How to solve graph coloring problem using backtracking, for example in {{youtube-timestamp 593}}? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:10:52.476Z
	  card-last-reviewed:: 2024-02-23T02:10:52.477Z
	  card-last-score:: 5
		- Find first possible solution for first few stages and solve all possible solutions for the last stage.
		- After all solution for last stage is recorded and then go one stage up and find another solution for the n-1 stage. And then repeat the first step.
		- This continues until the n-1 stage tried all possible color and then go one step up. Continue this until we reach the first stage.