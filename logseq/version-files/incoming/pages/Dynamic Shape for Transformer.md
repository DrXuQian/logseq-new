- #card What is autoregressive mean in transformer decoder?
  collapsed:: true
	- In the context of Transformer models, particularly with respect to the decoder, "autoregressive" refers to the process of generating one part of the sequence at a time, using what's been generated so far as context for generating the next part.
	- In the context of Transformer models, particularly with respect to the decoder, "autoregressive" refers to the process of generating one part of the sequence at a time, using what's been generated so far as context for generating the next part.
	- To dive deeper:
	- **Basics of Autoregression**: In time series analysis and statistics, an autoregressive model is one where the current value of a series is based on the previous values in the same series. A simple example is trying to predict the temperature for tomorrow based on the temperatures of the past few days.
	- **Autoregressive in the Transformer Decoder**:
	- When the Transformer model (like the original architecture used in "Attention is All You Need") is used for sequence-to-sequence tasks (e.g., machine translation), the decoder generates the output sequence in an autoregressive manner.
		- Initially, the decoder is given a start-of-sequence token.
		- It then predicts the next token based on this start token. Once the first token is predicted, it's fed back into the decoder as part of the input to predict the second token.
		- This process is repeated, with each subsequent token being conditioned on the previously generated tokens, until an end-of-sequence token is produced or some maximum sequence length is reached.
	- **Importance of Masking**: During training, we usually know the target sequence in advance. Therefore, to ensure that the prediction of each token only depends on the previous tokens (and not the future ones), the self-attention mechanism in the decoder must be appropriately masked. This is done using a causal or look-ahead mask which ensures that, at any position, the model can only attend to earlier positions in the sequence.
	- **Efficiency Considerations**: Autoregressive generation is inherently sequential, which means generating long sequences can be time-consuming. Some methods have been proposed in recent years to parallelize this process or make it more efficient.
	- In conclusion, the term "autoregressive" in the Transformer's decoder context means that the model generates the output sequence one token at a time, using previously generated tokens as context for generating subsequent tokens.
- Constraints:
  collapsed:: true
	- DMA tasks operate on compressed data, so cannot be dynamically resized.  This means it's not feasible to scale down a network based on the number of tokens in the current inference.
	- To handle dynamic tensor sizing based on input token count, an input tensor is broken into primitives where each primitive is compressed, and primitives can be dynamically included or omitted based on scaling factor.