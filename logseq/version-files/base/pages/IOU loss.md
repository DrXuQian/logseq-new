---
title: IOU loss
---
- naïve iou loss:
	 - ![](../assets/4iBR_Cnzor.png)
		 - 两个框不相交时，无法反向传播
		 - 两个框的IOU相同时的多种情况
			 - ![](../assets/Nffdzbf0V0.png)
- giou loss:
	 - ![](../assets/GkcV1cXzj-.png)
		 - 两个框同时在内部时，无法区分
			 - ![](../assets/82TeYdZ7_v.png)
- diou loss:
	 - ![](../assets/EhYaHhxrwa.png)
		 - 预测框中心点一致，并且同时都在内部时：
			 - ![](../assets/e2udAfEs2v.png)
- ciou loss:
	 - ![](../assets/HMku5bplsH.png)
	 - ![](../assets/c3QBHPzNX8.png)