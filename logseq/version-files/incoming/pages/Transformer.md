---
title: Transformer
---

- References
	- [[Paper: Attention is all you need]]
	- [The Illustrated Transformer – Jay Alammar – Visualizing machine learning one concept at a time.](https://jalammar.github.io/illustrated-transformer/)
- Recall Questions
	- What is the structure of Transformers? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:14:01.382Z
	  card-last-reviewed:: 2023-01-28T15:14:01.383Z
	  card-last-score:: 5
		- ![](../assets/u80P1G2_Bg.png)
	- How to represent the input sentence in networks? #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:23.057Z
	  card-last-reviewed:: 2023-01-28T15:13:23.057Z
	  card-last-score:: 5
		- Through tokenization of the words in the sentence.
		- The order is irrelevant of the tokens.
	- What is the word embedding in Transformer?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:39.310Z
	  card-last-reviewed:: 2023-01-28T15:11:39.311Z
	  card-last-score:: 5
		- continuous valued vectors representing the tokens.
		- tokens with similar meanings tend to be closer in embedding space.
	- Why we need positional encodings in Transformer? #ques1 #card
	  id:: ce18c608-5413-4894-8d86-8dc6ba2feaa8
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:31.489Z
	  card-last-reviewed:: 2023-01-28T15:11:31.490Z
	  card-last-score:: 5
		- the tokens after emedding have no order information while it might affect the sentence meaning
		- 如果没有positional encoding，对于class token，交换其他的token embedding的顺序不影响class token的计算结果。
			- 比如Qian kill you经过embedding之后得到$$T_1, T_2, T_3$$的embedding，
			- you kill Qian 得到$$T_3, T_2, T_1$$的embedding，计算时，class token的结果是一样的，但是显然这两个句子意思不一样。
		- detailed positional encoding in transformer
			- add the postional encoding to the tokenization embedding
			- ![](../assets/5FOqLlrRyE.png)
	- {{embed  ((0dfae322-2c2f-4fa8-b43e-dfbb902bccd2))}}
	- {{embed  ((28e250b0-c4a1-477f-ac3b-561c0d970f17))}}
	- What is the structure of transformer encoder?  #ques1 #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:12:10.400Z
	  card-last-reviewed:: 2023-01-28T15:12:10.400Z
	  card-last-score:: 5
		- ![](../assets/Q_bwsYdsTc.png)
- Notes
	- A high level overview
		- Overview
			- ![](../assets/SpzyJBAny5.png)
		- Encoder and decoder in transformer
			- ![](../assets/pwdX5smGZN.png)
		- 6 encoders and 6 decoders form the transformer
			- ![](../assets/pbHMK87oGC.png)
		- Encoder
			- ![](../assets/sf3kg2zpVi.png)
		- Decoder
			- ![](../assets/lwVzoUhYLV.png)
	- Embedding
		- each word is represented in vector size of 512
			- ![](../assets/NNh2NNHMCu.png)
		- after embedding the words, we feed them into the encoder
			- ![](../assets/CktyQZFya7.png)
				- > Here we begin to see one key property of the Transformer, which is that the word in each position flows through its own path in the encoder. There are dependencies between these paths in the self-attention layer. The feed-forward layer does not have those dependencies, however, and thus the various paths can be executed in parallel while flowing through the feed-forward layer.
	- Encoding
		- ![](../assets/qwUGHuw0NF.png)
	- self-attention
		- High-level overview
			- ![](../assets/k066a5ql2r.png)
		- In-detail
			- **first step**, create three vector $$Q,V,K$$
				- By multiplying the embedding with three matrices $$W_Q, W_K, W_V$$
				- Smaller in dimensions than embedding vectors (512 --> 64)
				- ![](../assets/xHUlWhLtr8.png)
			- **second step**, calculating self-attention
				- dot product of Query vector and Key vector
					- ![](../assets/-E_k6eoySv.png)
			- **third and fourth step**, divided the scores by 8 (the square root of the dimension of the key vectors used in the paper – 64. This leads to having more stable gradients. There could be other possible values here, but this is the default)
			  Then pass through a softmax function to get the normalized score
				- ![](../assets/bIBtY-h9w3.png)
			- **fifth step**, multipy each value vector by the softmax score
			- **sixth step**, sum the weighted value vectors
				- ![](../assets/C2G6-x_sPx.png)
			-
	- Matrix calculation of self-attention
		- ![](../assets/SzfFQlhJ_h.png)
		- ![](../assets/ee6-qWAij4.png)
	- Mutil-head attention
		- benefit
			- expands the model's ability to focus on different positions
		- structure
			- ![](../assets/wiO67p_X9E.png)
		- 把输入拆成$$h$$个size为$$N*d_k$$的sequences ($$d_{model} = h * d_k$$) ($$h=8$$)
			- ![](../assets/ZlaIhVPj8M.png)
		- Concat the output, multiply by $$W_O$$, feed into FFN
			- ![](../assets/uMpjt4ncM2.png)
		- summary
			- ![](../assets/M97H4rI4-I.png)
		- visualize the attention
			- ![](../assets/YhVxVFlZds.png)
	- Positional encoding
		- Transformer 的运算中没有考虑不同sequence的顺序，positional encoding
		- ((9117b16d-95fb-4272-888f-609813fdd43f))
		- ((ce18c608-5413-4894-8d86-8dc6ba2feaa8))
		- > To address this, the transformer adds a vector to each input embedding. These vectors follow a specific pattern that the model learns, which helps it determine the position of each word, or the distance between different words in the sequence. The intuition here is that adding these values to the embeddings provides meaningful distances between the embedding vectors once they’re projected into Q/K/V vectors and during dot-product attention.
		- ![](../assets/JmQEjfoLui.png)
		- For example, suppose the embedding have dimension of 4
			- ![](../assets/wBduSgxRY1.png)
		- Visualize of the position embedding
			- A real example of positional encoding for 20 words (rows) with an embedding size of 512 (columns). You can see that it appears split in half down the center. That's because the values of the left half are generated by one function (which uses sine), and the right half is generated by another function (which uses cosine). They're then concatenated to form each of the positional encoding vectors.
				- ![](../assets/D8Iy016jrm.png)
			- ```python
			  @expert_utils.add_name_scope()
			  def get_timing_signal_1d(length,
			                     channels,
			                     min_timescale=1.0,
			                     max_timescale=1.0e4,
			                     start_index=0):
			    """Gets a bunch of sinusoids of different frequencies.
			    Each channel of the input Tensor is incremented by a sinusoid of a different frequency and phase.
			    This allows attention to learn to use absolute and relative positions.
			    Timing signals should be added to some precursors of both the query and the memory inputs to attention.
			    The use of relative position is possible because sin(x+y) and cos(x+y) can be
			    expressed in terms of y, sin(x) and cos(x).
			    In particular, we use a geometric sequence of timescales starting with
			    min_timescale and ending with max_timescale.  The number of different
			    timescales is equal to channels / 2. For each timescale, we
			    generate the two sinusoidal signals sin(timestep/timescale) and
			    cos(timestep/timescale).  All of these sinusoids are concatenated in
			    the channels dimension.
			    Args:
			      length: scalar, length of timing signal sequence.
			      channels: scalar, size of timing embeddings to create. The number of
			    different timescales is equal to channels / 2.
			      min_timescale: a float
			      max_timescale: a float
			      start_index: index of first position
			    Returns:
			      a Tensor of timing signals [1, length, channels]
			    """
			    position = tf.to_float(tf.range(length) + start_index)
			    num_timescales = channels // 2
			    log_timescale_increment = (
			  math.log(float(max_timescale) / float(min_timescale)) /
			  (tf.to_float(num_timescales) - 1))
			    inv_timescales = min_timescale * tf.exp(
			  tf.to_float(tf.range(num_timescales)) * -log_timescale_increment)
			    scaled_time = tf.expand_dims(position, 1) * tf.expand_dims(inv_timescales, 0)
			    signal = tf.concat([tf.sin(scaled_time), tf.cos(scaled_time)], axis=1)
			    signal = tf.pad(signal, [[0, 0], [0, tf.mod(channels, 2)]])
			    signal = tf.reshape(signal, [1, length, channels])
			    return signal
			  ```
	- Encoder
		- ![](../assets/1OhLcAj2uF.png)
	- Decoder
		- ![](../assets/2r5_jfhCeL.png)
	- Process
		- The output of Encoder2 would go to both Decoder1 and Decoder2.
		- Note that in Decoder, we calculate the encoder-decoder attention
			- The “Encoder-Decoder Attention” layer works just like multiheaded self-attention, except it creates its Queries matrix from the layer below it, and takes the Keys and Values matrix from the output of the encoder stack.
		- The self attention layers in the decoder operate in a slightly different way than the one in the encoder:
			- In the decoder, the self-attention layer is only allowed to attend to earlier positions in the output sequence. This is done by masking future positions (setting them to -inf) before the softmax step in the self-attention calculation.
		- visualization of the process
			- for the first output token in Decoder side:
				- ![](../assets/U2Uu0spQSQ.gif)
				-
			- for the next outputs in Decoder side:
				- ![](../assets/VmHm2DEYJ-.gif)
	- Final Linear and Softmax layer
		- ![](../assets/8xsQ14LxIf.png)
- Summary
	- #towrite