---
title: C++: set
---
- 
```c++
template < class T,                        // set::key_type/value_type
           class Compare = less<T>,        // set::key_compare/value_compare
           class Alloc = allocator<T>      // set::allocator_type
           > class set;
```
- set
	 - declaration
		 - 
```c++
set<int, greater<int> > s1;
// insert elements in random order
s1.insert(40);
s1.insert(30);
s1.insert(60);
s1.insert(20);
s1.insert(50);

set<int> s2(s1.begin(), s1.end());

// remove all elements up to 30 in s2
s2.erase(s2.begin(), s2.find(30));

// remove element with value 50 in s2
num = s2.erase(50);



```
	 - Loop
		 - 
```c++
    for (itr = s1.begin(); itr != s1.end(); itr++)
    {
        cout << *itr<<" ";
    }
```