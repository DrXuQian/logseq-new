---
title: hard negative mining
---
- During training, as most of the bounding boxes will have low IoU and therefore be interpreted as __negative __training examples, we may end up with a disproportionate amount of negative examples in our training set. Therefore, instead of using all negative predictions, it is advised to keep a ratio of negative to positive examples of around 3:1. The reason why you need to keep negative samples is because the network also needs to learn and be explicitly told what constitutes an incorrect detection.
- ![](https://miro.medium.com/max/1050/1*2v2i4IYlOCJJ5k1hKiHfHg.png)