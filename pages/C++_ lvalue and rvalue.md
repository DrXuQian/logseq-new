---
title: C++: lvalue and rvalue
---
- {{youtube https //www.youtube.com/watch?v=fbYknr-HPYE&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=85}}
	 - lvalue is a value with location in memory
	 - lvalue can be at the right of the equation, so rvalue is not always on the right of the equal sign
	 - temporary value is rvalue, like the return of some function
	 - if you return the reference of a static variable from a function, you can assign value to this lvalue reference
	 - Rvalue can be allowed to pass to a const &,
		 - The C++ language says that a local const reference prolongs the lifetime of temporary values until the end of the containing scope, but saving you the cost of a copy-construction (i.e. if you were to use an local variable instead).
- References
	 - https://nettee.github.io/posts/2018/Understanding-lvalues-and-rvalues-in-C-and-C/
	 - http://thbecker.net/articles/rvalue_references/section_01.html
		 - **Introduction**
			 - An __lvalue__ is an expression that refers to **a memory location and allows us to take the address of that memory location via the & operator**. An __rvalue__ is an expression that is not an lvalue.
				 - examples code
				 - 
```c++
  // lvalues:
  //
  int i = 42;
  i = 43; // ok, i is an lvalue
  int* p = &i; // ok, i is an lvalue
  int& foo();
  foo() = 42; // ok, foo() is an lvalue
  int* p1 = &foo(); // ok, foo() is an lvalue

  // rvalues:
  //
  int foobar();
  int j = 0;
  j = foobar(); // ok, foobar() is an rvalue
  int* p2 = &foobar(); // error, cannot take the address of an rvalue
  j = 42; // ok, 42 is an rvalue
```
		 - **Move Semantics**
			 - [[Class with pointers]]
				 - originally we would need to make a deep copy of the resources the pointer in rhs pointers to.
					 - const & 可以take both lhs和rhs，因为const & 意味着在这个function内部，该传入的参数不会被改变
					 - 
```c++
X& X::operator=(X const & rhs)
{
  // [...]
  // Make a clone of what rhs.m_pResource refers to.
  // Destruct the resource that m_pResource refers to. 
  // Attach the clone to m_pResource.
  // [...]
}
```
				 - For the following example, the last line used the overloaded assignment operators above and do the following:
					 - 1. Clone the resources return by foo()
					 - 2. Deconstruct whatever x is holding and replace with the clone
					 - 3. Deconstruct temporary class of foo()
					 - 
```c++
X foo();
X x;
// perhaps use x in various ways
x = foo();
```
				 - We can replace the upper operations with a simple swap operation and let the temporary deconstructor do the deconstruction
					 - mystery here is T&&
					 - 
```javascript
X& X::operator=(<mystery type> rhs)
{
  // [...]
  // swap this->m_pResource and rhs.m_pResource
  // [...]  
}
```