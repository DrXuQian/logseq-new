---
title: C++: iterator traits
---
- https://www.codeproject.com/Articles/36530/An-Introduction-to-Iterator-Traits
- STL的目的在于将数据结构和算法分开
	 - 数据结构由[[C++: queue vs deque vs stack]] [[C++: vector initialization]] 等结构来表示
	 - 数据结构与算法之间的连接由iterator来表示
		 - Containers: These are used to manage collection of objects of a certain kind. Containers can be of two types: [[Sequence Containers]] (vector, deque, list) and [[Associative Containers]] (Set, Multiset, Map, Multimap).
		 - Iterator: These are used to step through the elements of collection of objects (aka containers).
		 - Algorithms: These are used to process the elements of a collection. That is algorithms feed from containers and process those elements in a predefined way and may also push the results into the same/different container.
- Traits
	 - 问题在于算法没有办法获取iterator指向的数据类型是什么，hence 我们需要traits
		 - > 在算法中我们可能会定义简单的中间变量或者设定算法的返回变量类型，这时候需要知道迭代器所指元素的类型是什么，但是由于没有 typeof 这类判断类型的函数,我们无法直接获取
	 - STL iterator basic types:
		 - 
```c++
//@{
  /**
   *  @defgroup iterator_tags Iterator Tags
   *  These are empty types, used to distinguish different iterators.  The
   *  distinction is not made by what they contain, but simply by what they
   *  are.  Different underlying algorithms can then be used based on the
   *  different operations supported by different iterator types.
  */
  ///  Marking input iterators.
  struct input_iterator_tag {};
  ///  Marking output iterators.
  struct output_iterator_tag {};
  /// Forward iterators support a superset of input iterator operations.
  struct forward_iterator_tag : public input_iterator_tag {};
  /// Bidirectional iterators support a superset of forward iterator
  /// operations.
  struct bidirectional_iterator_tag : public forward_iterator_tag {};
  /// Random-access iterators support a superset of bidirectional iterator
  /// operations.
  struct random_access_iterator_tag : public bidirectional_iterator_tag {};
  //@} 
```
	 - Iterator traits在STL源码中的定义：
		 - 
```c++
template <class T>
struct iterator_traits {
  typedef typename T::value_type value_type;
  typedef typename T::difference_type difference_type;
  typedef typename T::iterator_category iterator_category;
  typedef typename T::pointer pointer;
  typedef typename T::reference reference;
};
```
	 - 我们可以通过`iterator_traits<T>::value_type`来萃取iterator中存储的数据结构的类型
	 - 这种结构保证了
		 - 1. 每一个iterator的定义都包含了必须的type
		 - 2. 保证了内嵌类型也能够通过iterator来访问
			 - 
```c++
namespace std {
    template <class T>
    struct iterator_traits<T*> {
        typedef T                          value_type;
        typedef ptrdiff_t                  difference_type;
        typedef random_access_iterator_tag iterator_category;
        typedef T*                         pointer;
        typedef T&                         reference;
    };
}
```
- 使用场景：
	 - template中只有iterator type，可以通过`typedef typename iterator_traits<MyIterator>::value_type value_type;` 来获取
	 - 
```c++
template <class MyIterator>
void do_something(MyIterator start, MyIterator end) {
    typedef typename iterator_traits<MyIterator>::value_type value_type; 
    value_type v = *start;
    .....
}
```
-