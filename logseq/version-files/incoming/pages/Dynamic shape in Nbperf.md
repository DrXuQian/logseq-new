- Dynamic shape in GPT2:
	- autoregressive decoder, kv cache
	- First time inference
	- Following inference
		- Dynamism:
			- Past KV cached size
- Changes in Nbperf:
	- Past + 1 for concat output, need to be able to inference this equation
	  logseq.order-list-type:: number
	- Sanity check for operators that are dynamic
	  logseq.order-list-type:: number
	- Behavior of dynamic shape when the shape is not dynamic
	  logseq.order-list-type:: number
	- L2 passes that might have impact (insert copy) need to adjust to fit in dynamism
	  logseq.order-list-type:: number
-