---
title: unordered_set
---

- [[217-Contains Duplicate]]
- [[217. 存在重复元素]]
- C++11
	- insert()插入元素
	- find(x)
		- ```c++
		   iterator find ( const key_type& k );
		  const_iterator find ( const key_type& k ) const;
		  ```
		- return value
			- search for the element with k as value and return the iterator to that value if found.
				- you can use the dereference to get the value from the iterator
			- return the end of the iterator if not found.
				- iterator end doesn't point to any specific value.
				- don't dereference the iterator end
	- count(x)
		- return the count of x in unordered set