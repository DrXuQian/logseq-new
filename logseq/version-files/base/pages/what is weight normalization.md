---
title: what is weight normalization
---
- References:
	- [[Normalization methods]] [[Paper: WDSR]]
- [[Recall questions]]
	- What is weight normalization? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:05.104Z
	  card-last-reviewed:: 2023-01-28T15:14:05.105Z
	  card-last-score:: 5
		- decouple the length from the direction and train these parameters seperately.
		- ![](../assets/q0NGvqTlJg.png)
	- WHat is the difference between bn and wn? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:44.310Z
	  card-last-reviewed:: 2023-01-28T15:13:44.310Z
	  card-last-score:: 5
		- bn inapplicable for RNN/LSTM because we need seperate bn for each recursive input.
		- wn is unstable in training.
	- What is the benefit of wn? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:48.580Z
	  card-last-reviewed:: 2023-01-28T15:12:48.580Z
	  card-last-score:: 5
		- wn stays the same when test and train distribution are different.
	- Why can we use larger learning rate in wn? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:03.033Z
	  card-last-reviewed:: 2023-01-28T15:11:03.033Z
	  card-last-score:: 5
		- wn stables the training process.
- Summary:
	- 将权重分解成g和v，分别训练