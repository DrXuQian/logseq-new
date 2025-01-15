---
title: 读书笔记: 算法刷题Leetcode中文版
---
- 第一章 编程技巧
	 - 1. 判断浮点数是否相等
		 - abs(a - b) < 1e-9 instead of a == b
	 - 2. 判断奇偶数，要用x%2 != 0，而不是x%2==1
	 - 3. 用char作为数组下标，要考虑char可能是负数。
	 - vector和string优先于动态分配的数组
		 - 
```c++
vector<vector<int>> arr1 = vector<vector<int>>(row_num, vector<int>(column, 0));
vector<vector<int>> arr2(row_num, vector<int>(column, 0));
```
	 - 使用reserve来避免不必要的重新分配
		 - [[C++: vector]]
- 第二章 线性表
	 - 2.1 数组
		 - 2.1.1 remove duplicates from sorted array
			 - 代码1
				 - 因为是sorted array，所以可以用用双指针。
		 - 2.1.2 remove duplicates from sorted array II
			 - 加一个额外的变量记录出现的次数
		 - 2.1.3 search in rotated sorted array
			 - 二分法
		 - 2.1.4 search in rotated sorted array II
			 - l++
		 - 2.1.5 median of two sorted arrays
		 -