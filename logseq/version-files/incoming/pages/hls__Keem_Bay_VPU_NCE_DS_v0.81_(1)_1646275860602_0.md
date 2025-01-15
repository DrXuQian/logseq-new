file-path:: ../assets/Keem_Bay_VPU_NCE_DS_v0.81_(1)_1646275860602_0.pdf
file:: [Keem_Bay_VPU_NCE_DS_v0.81_(1)_1646275860602_0.pdf](../assets/Keem_Bay_VPU_NCE_DS_v0.81_(1)_1646275860602_0.pdf)
title:: hls__Keem_Bay_VPU_NCE_DS_v0.81_(1)_1646275860602_0

- Above shoos the DPU subsystem – Each DPU contains 256 MPE's in a 16x16 confguraton. Each PMEgenerates points for a single output channel. Supported confguratons of the MPE's are 4x4x16 or 16x1x16
  hl-page:: 7
  ls-type:: annotation
  id:: 62202de2-20b1-4530-bc61-8993f0e52c47
- Each output tensor is described by the follooing elements:1.A storage element pointer array – the array defnes the physical memory locaton of the start ofeach storage element of the tensor.2.A sparsity element array – the array defnes the physical memory locaton of sparsity – this canaccessed directly as it is in dense format. SE and sparsity must aloays be in the sameorientaton.3.A SE memory bufers – this is a set of memory bufers assigned by SW for storage of SE's. TheODU is assigned 16 memory bufers one for each context. Multple SE's oill be packed in a singlememory bufer hooever SE's oill be 16B aligned oithin the memory bufer.
  ls-type:: annotation
  hl-page:: 12
  id:: 625cb665-a700-4e65-b861-42b19b5dc214