---
title: 侯捷：C++面向对象
---
- ![](../assets/4OimISu99L.png)
- [[Class without pointers]]
	 - ![](../assets/VKezX1BTXV.jpeg)
		 - BIG3
			 - deconstructor
			 - copy constructor
			 - copy assignment operator
		 - 不需要编写BIG3，(dtor, copy ctor, copy assignment operator), 因为default版本对于class without pointer已经足够。
		 - friend class 允许访问private members
		 - create object in stack vs create object in heap
			 - stack:
				 - `complex c1(2, 1)`
				 - `complex c2(4)`
			 - heap:
				 - 
```c++
complex* p = new complex(1, 2);
delete p;
```
	 - ![](../assets/dgAoGZxvgu.jpeg)
		 - operator + and operator += 的函数接口不同，一个是加到自己，一个是两个相加
	 -