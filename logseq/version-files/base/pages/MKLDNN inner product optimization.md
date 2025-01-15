---
title: MKLDNN inner product optimization
---
- Proof for MLDNN usage of padding zero
	 - line 330 in src/cpu/x64/jit_brgemm_conv.cpp
		 - ` wei_ic_sz = (dim_t)rnd_up(jcp.ic, last_ic_block) * jcp.oc_block;`
-