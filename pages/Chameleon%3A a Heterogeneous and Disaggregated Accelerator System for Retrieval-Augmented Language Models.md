- https://ar5iv.labs.arxiv.org/html/2310.09949
- #card What is a retrieval-augmented language model (RALM)?
	- ![Refer to caption](https://ar5iv.labs.arxiv.org/html/2310.09949/assets/fig/RALM.png)
	-
- Short Summary:
	- HW design for RALM system. Implement retrieval accelerator on FPGA.
- Summary:
	- This paper propose a HW design for accelerating RALM (Retrieval-Augmented Language Model). RALM consists of (a) retrieving context-specific knowledge from an external database and (b) actual LM model inference.
	  The paper implements retrieval accelerators on FPGAs and assigns LM inference to GPUs, with a CPU server orchestrating these accelerators over the network. Achieve >20x speed up compared with orginal CPU-based and CPU-GPU vector search systems.