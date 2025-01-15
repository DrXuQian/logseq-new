tags:: [[Computer Science - Computer Vision and Pattern Recognition]], [[Computer Science - Machine Learning]]
date:: [[Nov 30th, 2021]]
extra:: arXiv: 2106.13700
title:: @ViTAS: Vision Transformer Architecture Search
item-type:: [[journalArticle]]
access-date:: 2022-04-19T12:12:46Z
original-title:: ViTAS: Vision Transformer Architecture Search
url:: http://arxiv.org/abs/2106.13700
short-title:: ViTAS
publication-title:: arXiv:2106.13700 [cs]
authors:: [[Xiu Su]], [[Shan You]], [[Jiyang Xie]], [[Mingkai Zheng]], [[Fei Wang]], [[Chen Qian]], [[Changshui Zhang]], [[Xiaogang Wang]], [[Chang Xu]]
library-catalog:: arXiv.org
links:: [Local library](zotero://select/library/items/KSRTA48T), [Web library](https://www.zotero.org/users/9063164/items/KSRTA48T)
- [[Abstract]]
	- Vision transformers (ViTs) inherited the success of NLP but their structures have not been sufficiently investigated and optimized for visual tasks. One of the simplest solutions is to directly search the optimal one via the widely used neural architecture search (NAS) in CNNs. However, we empirically find this straightforward adaptation would encounter catastrophic failures and be frustratingly unstable for the training of superformer. In this paper, we argue that since ViTs mainly operate on token embeddings with little inductive bias, imbalance of channels for different architectures would worsen the weight-sharing assumption and cause the training instability as a result. Therefore, we develop a new cyclic weight-sharing mechanism for token embeddings of the ViTs, which enables each channel could more evenly contribute to all candidate architectures. Besides, we also propose identity shifting to alleviate the many-to-one issue in superformer and leverage weak augmentation and regularization techniques for more steady training empirically. Based on these, our proposed method, ViTAS, has achieved significant superiority in both DeiT- and Twins-based ViTs. For example, with only $1.4$G FLOPs budget, our searched architecture has $3.3\%$ ImageNet-$1$k accuracy than the baseline DeiT. With $3.0$G FLOPs, our results achieve $82.0\%$ accuracy on ImageNet-$1$k, and $45.9\%$ mAP on COCO$2017$ which is $2.4\%$ superior than other ViTs.
- [[Attachments]]
	- [Full Text](https://arxiv.org/pdf/2106.13700.pdf) {{zotero-imported-file ANNUAPAW, "Su et al. - 2021 - ViTAS Vision Transformer Architecture Search.pdf"}}
	- [arXiv.org Snapshot](https://arxiv.org/abs/2106.13700) {{zotero-imported-file TAKVBAUF, "2106.html"}}
- tags:: [[Computer Science - Computer Vision and Pattern Recognition]], [[Computer Science - Machine Learning]]
  date:: [[Nov 30th, 2021]]
  extra:: arXiv: 2106.13700
  title:: @ViTAS: Vision Transformer Architecture Search
  item-type:: [[journalArticle]]
  access-date:: 2022-04-19T12:12:46Z
  original-title:: ViTAS: Vision Transformer Architecture Search
  url:: http://arxiv.org/abs/2106.13700
  short-title:: ViTAS
  publication-title:: arXiv:2106.13700 [cs]
  authors:: [[Xiu Su]], [[Shan You]], [[Jiyang Xie]], [[Mingkai Zheng]], [[Fei Wang]], [[Chen Qian]], [[Changshui Zhang]], [[Xiaogang Wang]], [[Chang Xu]]
  library-catalog:: arXiv.org
  links:: [Local library](zotero://select/library/items/KSRTA48T), [Web library](https://www.zotero.org/users/9063164/items/KSRTA48T)
- [[Abstract]]
	- Vision transformers (ViTs) inherited the success of NLP but their structures have not been sufficiently investigated and optimized for visual tasks. One of the simplest solutions is to directly search the optimal one via the widely used neural architecture search (NAS) in CNNs. However, we empirically find this straightforward adaptation would encounter catastrophic failures and be frustratingly unstable for the training of superformer. In this paper, we argue that since ViTs mainly operate on token embeddings with little inductive bias, imbalance of channels for different architectures would worsen the weight-sharing assumption and cause the training instability as a result. Therefore, we develop a new cyclic weight-sharing mechanism for token embeddings of the ViTs, which enables each channel could more evenly contribute to all candidate architectures. Besides, we also propose identity shifting to alleviate the many-to-one issue in superformer and leverage weak augmentation and regularization techniques for more steady training empirically. Based on these, our proposed method, ViTAS, has achieved significant superiority in both DeiT- and Twins-based ViTs. For example, with only $1.4$G FLOPs budget, our searched architecture has $3.3\%$ ImageNet-$1$k accuracy than the baseline DeiT. With $3.0$G FLOPs, our results achieve $82.0\%$ accuracy on ImageNet-$1$k, and $45.9\%$ mAP on COCO$2017$ which is $2.4\%$ superior than other ViTs.
- [[Attachments]]
	- [Full Text](https://arxiv.org/pdf/2106.13700.pdf) {{zotero-imported-file ANNUAPAW, "Su et al. - 2021 - ViTAS Vision Transformer Architecture Search.pdf"}}
	- [arXiv.org Snapshot](https://arxiv.org/abs/2106.13700) {{zotero-imported-file TAKVBAUF, "2106.html"}}