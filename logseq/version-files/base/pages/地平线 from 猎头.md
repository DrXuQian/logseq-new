---
title: 地平线 from 猎头
---
- 综合总结
- 一面：揪一个项目问+Coding
	- 1、BN原理，有几个参数，训练和测试的区别，多卡时BN如何处理？了解其他的归一化操作吗（例如群组归一化？）；
	- 2、eg：MobileNet V1,V2原理，优化的方向；
	- 3、手写代码（NMS、BN、各种loss、卷积过程、ResNet）
		- [[NMS]]
	- Coding题参考：
		- [[206. 反转链表-Simple]] https://leetcode-cn.com/problems/reverse-linked-list/description/
			- ```c++
			  class Solution {
			  public:
			      ListNode reverseList(ListNode head) {
			    ListNode prev = nullptr;
			    ListNode curr = head;
			    while (curr != nullptr){
			        ListNode next = curr->next;
			        curr->next = prev;
			        prev = curr;
			        curr = next;
			    }
			    return prev;
			      }
			  };
			  ```
		- [[200. 岛屿数量-Medium]] https://leetcode-cn.com/problems/number-of-islands/
			- ```c++
			  class Solution {
			  public:
			      void polute_dfs(vector<vector<char>>& grid, int i, int j){
			    if (i < 0 || i >= grid.size()){
			        return;
			    }
			    if (j < 0 || j >= grid[0].size()){
			        return;
			    }
			    if (grid[i][j] == '2' || grid[i][j] == '0'){
			        return;
			    }
			    grid[i][j] = '2';
			    polute_dfs(grid, i - 1, j);
			    polute_dfs(grid, i + 1, j);
			    polute_dfs(grid, i, j + 1);
			    polute_dfs(grid, i, j - 1);
			      }
			      int numIslands(vector<vector<char>>& grid) {
			    int num_islands = 0;
			    for (int i = 0; i < grid.size(); i ++){
			        for (int j = 0; j < grid[0].size(); j ++){
			            if (grid[i][j] == '1'){
			                polute_dfs(grid, i, j);
			                num_islands ++;
			            }
			        }
			    }
			    return num_islands;
			      }
			  };
			  ```
		- [[70. 爬楼梯-easy]] https://leetcode-cn.com/problems/climbing-stairs/
		- [[剑指 Offer 58 - II. 左旋转字符串]] https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/
		- [[94. 二叉树中序遍历]] https://leetcode-cn.com/problems/binary-tree-inorder-traversal/
		- [[剑指 Offer 33. 二叉搜索树的后序遍历序列]] https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/
		- [[102. 二叉树层序遍历]] https://leetcode-cn.com/problems/binary-tree-level-order-traversal/
		- [[剑指 Offer 63. 股票的最大利润]] https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/
		- [[819. 最常见的单词]] https://leetcode-cn.com/problems/most-common-word/
		- [[209. 长度最小的子数组]] https://leetcode-cn.com/problems/minimum-size-subarray-sum/
		- [[407. 用 Rand7() 实现 Rand10()]] https://leetcode-cn.com/problems/implement-rand10-using-rand7/
		- 图的dfs
- 二面：简历项目细致化面试，为什么这么做？为什么不这么做？你们没有再做优化吗？
	- Eg
		- 1、人脸特征训练好，你们怎么检索的呢？直接暴力吗？SQL有没有优化？
		- 2、为什么用GAN，用GAN真的变好了吗？
		- 3、你们这个模型最后部署有没有考虑速度？有没有深入的优化一下？
	- ....
	- 最后可能会有几个简单的基础知识；
		- 1、kmeans聚类如何选择初始点。
		- 2、SVM的loss是啥。
		- 3、Triplet Loss 怎么生成那三个点
- 三面：整体综合面试
	- 1、最基本的骨干网络（VGG、GoogLenet、ResNet、MobileNet）聊聊；
	- 2、解决问题的思路聊聊；
	- 3、对团队的意向聊聊；
	- 4、目前面试的其他团队情况，对地平线的意向，是否知道过去要做什么聊聊；
- 具体细节
- 人选A（算子面工具链-上海）
	- 一面：介绍项目，coding：1、反转链表 2、岛屿数量
	- 二面：一个面试官，聊了1个半小时，其中50分钟聊经历，做了两道题（1、工作经历中写过的算子拿出来写一下 2、拓扑排序）。
	- 三面：今天聊了半小时，主要聊经历项目，面试官代稳。团队主要在南京，上海人少。其他没什么。
- 人选B（上海-视觉算法）
	- 一面：介绍项目
	- 二面：介绍项目，技术细节
	- 三面：项目大概内容，一道算法题，给一个不等概率随机数生成器，写一个等概率生成器
- 人选C(上海-视觉算法)
	- 一面：介绍项目，面试官问的问题偏个人理解。code:一题爬楼梯，用dp。一题图论，她直接说不会，面试官换了一道题，换成了string和数组相结合的一题。
	- 二面：介绍项目，技术细节，问了文本识别原理
- 人选D（北京-视觉算法）
	- 一面：介绍项目，项目比较多，所以面试官问得也比较多。code：卷积实现过程
- 人选E（上海-视觉算法）
	- 一面：项目，coding两题，一个是写3d点云体素化，一个是写nms
- 人选F（上海-视觉算法）
	- 一面：项目+小问题 （比如稀疏点云特征提取怎么提高结果），算法题几何题，给一个三维立方体，已知长宽高，求空间任一点到8个顶点的距离。
- 人选G（南京-算法加速）
	- 一面：具体以某一个网络为例，怎么调测性能和精度的， 发现的问题定位思路，优化思路
	- 题目：求最短连续子数组的长度，和等于目标值，算法主题写出来了
- 人选H（北京-嵌入式）
	- 一面：项目+coding四题：快速排序，单链表翻转，单例模式，生产者单个和多个消费者以及一个消费者多线程实现
	- 二面：面试官是做算子加速框架的，问了很多这方面的内容，人选工作接触的少，还写了两道题，第一题短板，工作里没接触；第二题写出来了；
- 人选I-深框
	- 一面：主要聊模型优化的内容，并基于简历再扩展问了些这方面的问题；
	- coding：数组里面找到和为一个目标值的最小连续子数组；
	- 二面：白纸手写了个卷积，然后重点讲量化和剪枝，代码问了一个图的dfs。
- 人选J-视觉
	- 一面：项目聊了一小时+coding30分钟两题
	- 第一个从字符串中寻找出现次数最多的单词，同时单词不属于禁闭列表当中。
	- 第二个爬楼梯，每一步爬1或2个台阶，给定总台阶数，求所有可能的爬楼梯组合
- 人选K-工具链
	- 一面：加速，训练部门，主要在模型加速这块儿的经验，人选经验适合在工具链方向。一道coding：二叉树
	- 二面：问了项目内容