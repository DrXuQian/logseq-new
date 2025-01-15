---
title: C++: vector
---
- vector的成员函数
	 - size：当前元素的个数
	 - capacity：最大容纳个数
	 - resize：强制将元素个数变为n个，调用构造或者析构，不影响cap。如果n>cap,则realloc.
	 - reserve：将cap改为至少n，如果n<cap，无动作，否则realloc。同时不改变size.
	 - > The [resize()](http://en.cppreference.com/w/cpp/container/vector/resize) method (**and passing argument to constructor is equivalent to that**) will insert or delete appropriate number of elements to the vector to make it given size (it has optional second argument to specify their value). It **will affect the size()**, iteration will go over all those elements, push_back will insert after them and you can directly access them using the operator[]. 
The [reserve()](http://en.cppreference.com/w/cpp/container/vector/reserve) method only allocates memory, but leaves it uninitialized.** It only affects capacity(), but size() will be unchanged.** There is no value for the objects, because nothing is added to the vector. If you then insert the elements, **no reallocation will happen,** because it was done in advance, but that's the only effect. 
So it depends on what you want. If you want an array of 1000 default items, use resize(). If you want an array to which you expect to insert 1000 items and want to avoid a couple of allocations, use reserve().