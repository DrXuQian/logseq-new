title:: vpux::MemLiveRangeInfo::addNewBuffer

## What doe this function do?
	- In general:
		- 主要修改了两个类成员变量
			- [[_allUsersInBlock]]
			- [[_reverseUsers]]
	- 1. 从[[_aliasInfo]]中获取所有的`aliasinfo[val]`，也就是当前输入val的所有aliases
	- 2. 遍历aliases，然后调用[[Operation *Region::findAncestorOpInRegion]]获取alias的`userAncestor`
	- 3. 插入到`_allUsersInBlock[val]`中
	- 4. 同时`_reverseUsers[userAncestor].insert(val);`
	- 这样既可以通过`val`来查询user的ancestor block，也可以通过ancestor block查到对应的`val`。
	- ## Questions
	- What is `userAncestor`? #card #vpux
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:13:58.040Z
	  card-last-reviewed:: 2023-01-28T15:13:58.041Z
	  card-last-score:: 5
		- ![image.png](../assets/image_1648428417215_0.png)
		- 对于%57，%56就是userAncestor，因为是第一个在这个block中的op
	-