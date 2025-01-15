---
title: C++: qualified name
---

- 限定名和非限定名
	 - 限定名(qualified name)，故名思义，是限定了命名空间的名称。看下面这段代码，cout和endl就是限定名：

	 - 
```c++
#include <iostream>
int main()  {
    std::cout << "Hello world!" << std::endl;}
```

	 - cout和endl前面都有`std::`，它限定了std这个命名空间，因此称其为限定名。

	 - 如果在上面这段代码中，前面用`using std::cout;`或者`using namespace std;`，然后使用时只用cout和endl，它们的前面不再有空间限定`std::`，所以此时的cout和endl就叫做非限定名(unqualified name)。
