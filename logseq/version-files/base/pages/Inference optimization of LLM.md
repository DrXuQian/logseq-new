# Speculative execution for LLM
id:: 64f56410-4ff6-4d27-b896-5cb83cbbc7ec
collapsed:: true
	- Small model for draft and get the sequence of tokens, feed those through the big model in a batch.
	- Because the computation cost for one token and sequence of tokens is almost the same for the big model. If the tokens from the big model matches the small model, jack pot. But if that doesn't, you waste nothing but some extra compute when using the small model, but the layer is memory bounded anyway, so the cost is none.
	- collapsed:: true
	  > *Speculative execution for LLMs is an excellent inference-time optimization. It hinges on the following unintuitive observation: forwarding an LLM on a single input token takes about as much time as forwarding an LLM on K input tokens in a batch (for larger K than you might think). This unintuitive fact is because sampling is heavily memory bound: most of the "work" is not doing compute, it is reading in the weights of the transformer from VRAM into on-chip cache for processing. So if you're going to do all that work of reading in all those weights, you might as well apply them to a whole batch of input vectors. I went into more detail in an earlier thread:*[*https://twitter.com/karpathy/status/1691571869051445433…*](https://twitter.com/karpathy/status/1691571869051445433)* The reason we can't naively use this fact to sample in chunks of K tokens at a time is that every N-th token depends on what token we sample at time at step N-1. There is a serial dependency, so the baseline implementation just goes one by one left to right. Now the clever idea is to use a small and cheap draft model to first generate a candidate sequence of K tokens - a "draft". Then we feed all of these together through the big model in a batch. This is almost as fast as feeding in just one token, per the above. Then we go from left to right over the logits predicted by the model and sample tokens. Any sample that agrees with the draft allows us to immediately skip forward to the next token. If there is a disagreement then we throw the draft away and we accept that we've done some throwaway work (sampling the draft and the forward passing for all the later tokens). The reason this works in practice is that most of the time the draft tokens get accepted, because they are easy, so even a much smaller draft model gets them. As these easy tokens get accepted, we skip through those parts in leaps. The hard tokens where the big model disagrees "fall back" to original speed, but actually a bit slower because of all the extra work. So TLDR: this one weird trick works because LLMs are memory bound at inference time, in the "batch size 1" setting of sampling a single sequence of interest, that a large fraction of "local LLM" use cases fall into. And because most tokens are "easy". *
		- *References*
			- [*https://arxiv.org/abs/2302.01318*](https://arxiv.org/abs/2302.01318)
			- [*https://arxiv.org/abs/2211.17192*](https://arxiv.org/abs/2211.17192)
- # Full Stack Optimization of Transformer Inference: a Survey
	- https://arxiv.org/pdf/2302.14017.pdf
	- ### Abstract
		- The article surveys various approaches for efficient Transformer inference, including analysis of bottlenecks, hardware implications, optimization, and neural architecture search. The authors also perform a case study using **Gemmini**, an open-source deep neural network accelerator generator, and demonstrate that a full-stack co-design approach with the surveyed methods can yield significant speedups with minimal performance degradation for Transformer inference.
	- Links:
	  collapsed:: true
		- https://www.nebuly.com/blog/full-stack-optimization-of-transformer-inference-a-survey-part-1
		- https://www.nebuly.com/blog/full-stack-optimization-of-transformer-inference-a-survey-part-2
	- ### Encoder and Decoder Architectures
	  collapsed:: true
		- The Transformer is an architecture introduced for machine translation tasks, consisting of encoder and decoder components. In subsequent works, encoder-only and decoder-only versions were introduced. The encoder block processes the input sequence in parallel and is suitable for natural language understanding tasks, while the decoder block processes tokens sequentially and is suitable for natural language generation tasks. The complexity of the encoder block scales linearly with sequence length, while the complexity of the decoder block scales linearly for projection layers and quadratically for act-to-act matmuls.
	- ### Hardware Design
	- #[[ML for HW design]]
	- #### Overview of Typical DNN Accelerators
		- ![](https://global-uploads.webflow.com/6392f64685456376914037d5/6412e528c3de729ee0a44718_1_hOy78_ffqLsWKxduC0qKtw.webp){:height 383, :width 781}
		- A typical deep learning accelerator consists of:
		  collapsed:: true
			- off-chip DRAM for holding the weights and activations
			- on-chip global buffer memory (referred as *global buffer*) which needs to be large enough to hold a subset of the weights and inputs in order to feed the *processing elements* (PEs)
			- an array of PEs, each capable of performing MAC operations, and which often contains one or more small local memories called register files (RFs)
			- an internal *network-on-chip* (NoC) that transfers data between PEs
			- [[Scratchpad memory]]
		- Accumulator context is very crucial for data reuse in neural networks.
	- #### Hardware-Software Co-Design
		- With new integer implementations of I-BERT’s GELU, LayerNorm, and Softmax variants added to the baseline CNN accelerator, overall end-to-end BERT inference performance improved by 39.6x with no need for quantization or dequantization
	- ### Transformer specific optimization methods
	- #### Accelerating attention
	  collapsed:: true
		- token pruning:
		  collapsed:: true
			- Remove less effective tokens in the long sequence length to reduce sequence length
			- SpAtten: Rank tokens by the amount of attention they are getting from the other tokens in the input sentence. The tokens with least attention are pruned. A Top-K hardware engine can be employed to filter out the low-important tokens based on their attention score.
				- ![image.png](../assets/image_1693810446545_0.png){:height 367, :width 616}
				- HW design for top-K engine
					- ![image.png](../assets/image_1693810477952_0.png){:height 406, :width 813}
				- Cascade token pruning
					- ![image.png](../assets/image_1693811416589_0.png)
				- Importance score calculation, that is where top-k comes in
					- ![image.png](../assets/image_1693811473222_0.png)
				- Also introduced head pruning where the head with low importance score is pruned completely and local value pruning where the value with low score is not fetched since 0 multiplied by 0 is still 0.
			- DTA-Trans: Prune token based on attention first, then different bit-precision are allocated to each of the remaining tokens based on the significance.
		- attention score sparsity:
		  collapsed:: true
			- Most of the combinations of the query and key tokens are not semantically meaningful.
				- Quantize before QK*V
				- Dynamically generate sparsity map? By Top-K maybe?
					- Pipelined:
						- DPU: tile 0 attention score --> tile 1 attention score --> tile 2 attention score --> tile 3 attention score -->
						- DSP: --------------------------------tile 0 sparsity gen-------> tile1 sparsity gen ------> tile2 sparsity gen --------->
	- #### Nonlinear operations
	  collapsed:: true
		- function approximation
			- Softermax:
			  collapsed:: true
				- 2 instead of e
			- Look up table based method
	- #### Accelerating decoding
	  collapsed:: true
		- Masked attention, the mask is in triangle shape. #IDF
			- ![image.png](../assets/image_1693813697126_0.png){:height 533, :width 519}
		- Early exiting:
			- This method dynamically adjusts the depth of the decoder for each token generation by terminating the inference at a mid-layer and making a prediction using the intermediate hidden states, rather than waiting until the end layer. While being a well-explored technique for encoder models [192, 234], CALM [191] has only recently extended this methodology to decoder models. A major challenge in decoding tasks is that, unlike in encoding tasks, the generation of one token relies on the activations of all previous tokens, due to the attention mechanism. If a previous token is
			  exited early, then there is nothing to attend for the skipped layers. To address this issue, CALM proposes “state propagation,” which copies the activations of the final layer before exiting to all the skipped layers. This had a minimal impact on generation quality.
			- ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgLe71KgX8TB69O92Mg_O18YyzdpXOZErwaqPYtgtN_6rtbiPQkEHn0UFaI1tp88_fE_7uTHT7JxNdl05tM0GQ_UXp5OUSDSaRS8xNx-tATN56H2XlaUisDgvB6vKK5GxTpwig27Ud_Ay0MGpk-NMiKZe6CjIb2ZNYap7Dsoun-NlEsXpDnyuvwknl9kg/w640-h640/image2.gif)
		- ((64f56410-4ff6-4d27-b896-5cb83cbbc7ec))
	- ### Mapping transformers to Hardware
		- #### Graph-level
			- Layer fusion
			- Graph transformation
			- Sparsity/Quantization
		- #### Operator-level
			- spatial/temporal tiling
			- sequencing
			- pipelining
			- mapping
		- #### Finding performant mappings
			- Graph-level Schedulers
			- Operation-level Mappers
				- brute-force search
				- feedback-based search
				- constrained optimization
					- further reading of this
						-
					- ![image.png](../assets/image_1693817262015_0.png)
		- #### Performance Modeling of Mappings
			- Performance models can provide the mappers with performance feedback for different mappings without executing mappings on real hardware or running simulations on accelerators under development.