---
title: C++: template
---
- {{youtube https //www.youtube.com/watch?v=LMP_sxOaz6g}}
- template
	 - typename && class
		 - https://feihu.me/blog/2014/the-origin-and-usage-of-typename/
		 - brief history:
			 - > Stroustrup在最初起草模板规范时，他曾考虑到为模板的类型参数引入一个新的关键字，但是这样做很可能会破坏已经写好的很多程序（因为class已经使用了很长一段时间）。但是更重要的原因是，在当时看来，class已完全足够胜任模板的这一需求，因此，为了避免引起不必要的麻烦，他选择了妥协，重用已有的class关键字。所以只到ISO C++标准出来之前，想要指定模板的类型参数只有一种方法，那便是使用class。这也解释了为什么很多旧的编译器只支持class。
		 - 使用class的奇怪之处：
			 - class在类和模板中的意义不同
			 - typical usage:
				 - 
```c++
// implement strcmp-like generic compare function

// returns 0 if the values are equal, 1 if v1 is larger, -1 if v1 is smaller

template <typename T>
int compare(const T &v1, const T &v2)
{
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```
				 - 看上去这个template应该只支持用户自定义的class类型
				 - 
```c++
int v1 = 1, v2 = 2;
int ret = compare(v1, v2);
```
				 - 使用内置类型调用时会有些奇怪。
		 - 引入typename的真实原因
			 - 
```javascript
template <class T>
void foo() {
    T::iterator * iter;
    // ...

}

//instanitate foo
foo<ContainsAnotherType>();

//case 1
struct ContainsAType {
    struct iterator { /*...*/ };
    // ...

};
//case2
struct ContainsAnotherType {
    static int iterator;
    // ...

};
```
			 - > 同一行代码能以两种完全不同的方式解释，而且在模板实例化之前，完全没有办法来区分它们，这绝对是滋生各种bug的温床。这时C++标准委员会再也忍不住了
		 - intro of typename:
			 - A name used in a template declaration or definition and that is dependent on a template-parameter is assumed not to name a type unless the applicable name lookup finds a type name or the name is qualified by the keyword typename.
			 - 因此，如果你想直接告诉编译器`T::iterator`是类型而不是变量，只需用typename修饰：
			 - 
```c++
template <class T>void foo() {
    typename T::iterator * iter;
    // ...
}
```
	 -