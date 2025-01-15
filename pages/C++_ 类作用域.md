---
title: C++: 类作用域
---
- 在类外部访问类中的名称时，可以使用类作用域操作符，形如`MyClass::name`的调用通常存在三种：静态数据成员、静态成员函数和嵌套类型：
- 
```c++
struct MyClass {
    static int A;
    static int B();
    typedef int C;}
```
- `MyClass::A, MyClass::B, MyClass::C`分别对应着上面三种。