---
title: RCNN Family
---

- RCNN
	 - ![](../assets/WYWQVeV749.png)

	 - Selective search for 2k proposals (具有相似颜色直方图特征的区域进行递归融合)

	 - 经过CNN网络进行特征提取，最后进入一个SVM分类器进行分类

	 - 用线性回归器进行boundingbox的reg修正

	 - 最后对于所有的检测结果作[[NMS]]

- Fast-RCNN
	 - ![](../assets/cj4hmBcSAp.png)

	 - 用convolution network来作regions 的proposal

- Faster-RCNN
	 - ![](../assets/X7aXnaNbW8.png)

	 - ![](../assets/5Bbg1meX7O.png)

	 - ![](../assets/UL5g7GnyDy.png)

	 - [[Region proposal network]] [[RPN]]
		 - ![](../assets/Njvb2VlSs-.png)
			 - 第一条线，输出WxHx18，18 = 9([[anchor box]]for each pixel in feature map)x2(positive/negative)

			 - 第二条线，输出WxHx36，36 = 9 x 4(boundingbox regressor)

			 - proposal layer:
				 - 将boundingbox映射回MxN的大小，判断是否超出图像边界，超出部分截断

				 - 对于剩余的部分进行NMS

				 - 输出的proposal的大小是对应在MxN尺度下的boundingbox

	 - [[ROI pooling]]
		 - RPN中出来的proposal大小不一致，无法输入传统分类网络进行分类。

		 - ![](../assets/iXqHAhD5Or.png)
			 - 将proposal映射到WxH尺度下，否则无法截取featuremap

			 - 对于每个proposal位置的boundingbox划分为pooled_w 和pooled_h大小如图所示，最后对于每一个小方块进行max pooling

			 - [[spatial pyramid pooling]]

	 - 训练流程
		 - 在已经pretrain的backbone基础上训练RPN网络
			 - ![](../assets/BbIdwprla1.png)
				 - cls loss，即rpn_cls_loss层计算的softmax loss，用于分类anchors为positive与negative的网络训练

				 - reg loss，即rpn_loss_bbox层计算的soomth L1 loss，用于bounding box regression网络训练。
