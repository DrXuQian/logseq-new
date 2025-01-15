---
title: Paper: Neural architecture search with reinforcement learning
---

- Metadata
	 - [[Class]]: [[AutoML]] [[NAS]]

	 - [[Topic]]: NAS

	 - [[Lecturer]]: 

	 - [[Date]]: 

	 - [[Keywords]]: 

- Recall Questions
	 - Why use RNN + RL? Why not RL directly?
		 - 其实就是RL，用policy gradient 更新参数而已，跟proxylessnas一模一样。

		 - RNN在这里也完全可以替换为别的CNN，或者直接一个vector接softmax (proxylessnas)

- Notes
	 - 

- Summary
	 - Controller RNN
		 - ![](../assets/XDeh3mowkj.png)

		 - Train the controller RNN
			 - ![](../assets/W6wQZZLw7U.png)

			 - Challenge:
				 - RNN参数对于val acc 不可微分

	 - RL to train RNN
		 - State:
			 - hi, xi

		 - Reward:
			 - ValAcc for all steps as in AMC.

		 - ![](../assets/YzzyhE3fAy.png)
			 - 简单的来说就是通过梯度下降让reward较大的网络结构的概率变高。
