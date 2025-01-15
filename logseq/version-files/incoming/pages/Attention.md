---
title: Attention
---

- ## Recall Questions
  background-color:: #497d46
	- What is seq2seq learning? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:02.100Z
	  card-last-reviewed:: 2023-01-28T15:14:02.101Z
	  card-last-score:: 5
		- The goal of seq2seq learning is transform an input sequence to a new sequence.
			- The input and output can vary in length.
			- The input consists of tokens, which can be texts or pixels.
		- Typical example
			- translation from english to french
	- What is the structure of RNN encoders and decoders? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:42.854Z
	  card-last-reviewed:: 2023-01-28T15:13:42.854Z
	  card-last-score:: 5
		- ![](../assets/A5agC6Y6wf.png)
		- ![](../assets/t46mJinE0M.png)
	- What is the limitation of RNN compared to transformers? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:37.504Z
	  card-last-reviewed:: 2023-01-28T15:11:37.505Z
	  card-last-score:: 5
		- bottleneck problem
			- tend to focus on the latter tokens while the previous tokens might contain some important message
		- [[vanishing gradient in RNN]] #toread
			- https://distill.pub/2019/memorization-in-rnns/
	- What is attention? and why do we need attention to address the issue in RNN? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:50.656Z
	  card-last-reviewed:: 2023-01-28T15:12:50.656Z
	  card-last-score:: 5
		- direct connection to all timestamps instead of focusing on the latter ones in RNN.
		- attention networks look at all words at the same time and learn to pay more attention to the important ones
	- What is self-attention? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:26.375Z
	  card-last-reviewed:: 2023-01-28T15:12:26.375Z
	  card-last-score:: 5
		- 如果$$Q，K，V$$都是从同一个sequence计算得到，那么就叫做self-attention
		- 如果Query向量跟($$K,V$$)对不是同一个sequence得到，就不叫self-attention
			- And $$K，V$$对是成对的，来自同一个sequence
		- The attention to the sequence itself. Find the correlation between the different tokens of the input sequence
		- For example, love is more positively related to the token I and you than Hello.
		- ![](../assets/rFmD1FSiXl.png)
	- What is feature based attention? What does the query, value and key stands for? #ques1 #card
	  id:: 0dfae322-2c2f-4fa8-b43e-dfbb902bccd2
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:00.474Z
	  card-last-reviewed:: 2023-01-28T15:12:00.474Z
	  card-last-score:: 5
		- Example for understanding:
			- ![](../assets/RmDn6_vEDm.png)
			- think of the attention matrix as **where** to look and the Value matrix as **what** I actually want to get.
		- Scaled dot product attention:
			- ![](../assets/ZV5EzDZEUv.png)
			- ![](../assets/vEGfbN2jXe.png)
			- ![](../assets/beWiLHGtUr.png)
			- The $$Q$$ and $$K$$ product can be viewed as the matrix version of vector similarity
			- And $$\sqrt d_k$$ is the scaling factor to normalize the inner product dimension.
				- 缩放因子以避免点积带来的方差影响
				- And $$d_k$$ is the vector length of embedded tokens. $$d_k$$ is the same for different input sentences ($$N$$ is the number of words in a sentence).
			- And $$W_Q, W_K, W_V$$ all with shape($$d_k, d_{model}$$), $$X$$ with shape ($$N, d_k$$)
			- softmax is there to keep the output a probability distribution over different values
	- What is local-attention? What is the difference between local attention and global attention? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.46
	  card-next-schedule:: 2023-02-01T15:14:10.694Z
	  card-last-reviewed:: 2023-01-28T15:14:10.694Z
	  card-last-score:: 5
		- Local attention only calculate the attention between the input token with a limited number of other tokens.
		- This can reduce the memory consumption when the input sequence is very long.
		- ![](../assets/FDRQmVwDf0.png)
	- What is [[multi-head attention]]?  #ques1 #card
	  id:: 28e250b0-c4a1-477f-ac3b-561c0d970f17
	  card-last-interval:: 4
	  card-repeats:: 2
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:04.198Z
	  card-last-reviewed:: 2023-01-28T15:14:04.198Z
	  card-last-score:: 5
		- 其实就是`12x784x64`与`12x64x64`做matmul
			- 相当于heads数量为batch。不用认为是12个卷积之后concat。这两种理解是等价的。
		- 把输入拆成$$h$$个size为$$N*d_k$$的sequences ($$d_{model} = h * d_k$$)
		- Equation of multi-head attention:
			- ![](../assets/QCZDfh9XNf.png)
		- difference between scaled dot-product attention and multi-head attention:
			- Multi-head attention inputs are splited $$h$$ times to $$d_k$$ ($$d_{model} = h * d_k$$)
			- ![](../assets/6eRyqXeL7L.png)
- ## Notes
  background-color:: #533e7d
	- Sequence to sequence learning
		- Before attention and transformers, Sequence to Sequence (**Seq2Seq**) worked pretty much like this:
		  ![seq2seq](https://theaisummer.com/static/cd814b80c90e3ce6bef8a52b690d3eb6/c1b63/seq2seq.png)[🔗](https://theaisummer.com/static/cd814b80c90e3ce6bef8a52b690d3eb6/d5c6f/seq2seq.png)
		  The elements of the sequence $$x_1​,x_2$$​, etc. are usually called **tokens**. They can be literally anything. For instance, text representations, pixels, or even images in the case of videos.
		- The goal is to transform an input sequence (source) to a new one (target).
	- Attention to the rescue!
		- Attention was born in order to address these two things on the Seq2seq model. But how? The core idea is that the context vector $$z$$ should have access to all parts of the input sequence instead of just the last one. In other words, we need to form a **direct connection** with each timestamp. 
		  We can look at all the different words at the same time and learn to “pay attention“ to the correct ones depending on the task at hand. And behold. This is what we now call **attention**, which is simply a notion of **memory**, gained from attending at multiple inputs through time.
- ## Summary
  background-color:: #787f97
	- #towrite