---
title: MKLDNN
---
- 
```c++

```
	 -
- The final loop for computation of gemm_microkernel_avx512:
- 
```c++
            // in the test, ld_block2=4, bd_e=5, bd_b=1
			for (int ld = 0; ld < ld_block2; ld++) {
              	//is_ld_tail=False
                if (is_ld_tail) {
                    vmovups(load() | ld_tail_mask | T_z,
                            ptr[reg_aux_B + B_offset(ld, rd)]);
                } else {
                    vmovups(load(), ptr[reg_aux_B + B_offset(ld, rd)]);
                }
                for (int bd = bd_b; bd < bd_e; bd++) {
                    auto zmm = accm(ld_block2, bd, ld);
                    if (is_emdbd)
                        vfmadd231ps(zmm, load(),
                                zword_b[reg_aux_A + A_offset(bd, rd)]);
                    else
                        dot_product(zmm, load(), bcst(bd));
                }
            }
```
- `select_ic_block`
	 - ![](../assets/VjmQzxWGxU.png)
	 - 
```c++
void brg_blocking_t::select_ic_block() {
    auto max_simd_blocks = nstl::min(5 * simd_w, div_up(ic, simd_w));
    const auto est_ur = nstl::min(sp_block, estimate_ur(oc_block));
    const auto inp_ur = is_os_blocking ? est_ur : inp_w(est_ur, kw_block);

    if (kw_block > 1) {
        // try to fit src into L1
        const auto inp_per_ic = (unsigned int)inp_ur * src_dsz;
        max_simd_blocks = saturate(
                1, max_simd_blocks, (int)(L1 / (inp_per_ic * simd_w)));
    }
    {
        // try to fit all batch for ur into L2
      	// wei_per_ic is the size for the filter per input channel
        const auto wei_per_ic = (unsigned int)kd_block * kh_block * kw_block
                * oc_block * wei_dsz;
      	// inp_per_ic is the size for the input per
      	// inp_ur??
        const auto inp_per_ic
                = (unsigned int)kd_block * kh_block * inp_ur * src_dsz;
        const auto out_size = (unsigned int)ur * oc_block * dst_dsz;

        max_simd_blocks = saturate(1, max_simd_blocks,
                (int)((L2 - out_size) / ((wei_per_ic + inp_per_ic) * simd_w)));
    }

    auto nb_simd = utils::div_up(ic, simd_w);
    const auto nb_icb_eff_threshold = 0.5f;
    auto simd_blocks = 1;
    for (int nb_icb = nstl::min(max_simd_blocks, nb_simd); nb_icb >= 1;
            nb_icb--) {
        auto nb_icb_eff = (float)nb_simd / rnd_up(nb_simd, nb_icb);
        if (nb_icb_eff >= nb_icb_eff_threshold) {
            simd_blocks = nb_icb;
            break;
        }
    }
    ic_block = simd_blocks * simd_w;
    nb_ic = utils::div_up(ic, ic_block);
}
```
- vpdpbusd
	 -