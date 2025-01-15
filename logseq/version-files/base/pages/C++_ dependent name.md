---
title: C++: dependent name
---
- 依赖名(dependent name)是指依赖于模板参数的名称，而非依赖名(non-dependent name)则相反，指不依赖于模板参数的名称。看下面这段代码：
- 
```c++
template <class T>class MyClass {
    int i;
    vector<int> vi;
    vector<int>::iterator vitr;

    T t;
    vector<T> vt;
    vector<T>::iterator viter;};
```
- 因为是内置类型，所以类中前三个定义的类型在声明这个模板类时就已知。然而对于接下来的三行定义，只有在模板实例化时才能知道它们的类型，因为它们都依赖于模板参数T。因此，T, vector<T>和`vector<T>::iterator`称为依赖名。前三个定义叫做非依赖名。
- 更为复杂一点，如果用了typedef T U; U u;，虽然T没再出现，但是U仍然是依赖名。由此可见，不管是直接还是间接，只要依赖于模板参数，该名称就是依赖名。