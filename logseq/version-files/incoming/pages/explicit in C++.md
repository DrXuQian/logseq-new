- What is the explicit keyword in C++ mean? #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:13:51.586Z
  card-last-reviewed:: 2023-01-28T15:13:51.586Z
  card-last-score:: 5
	- To avoid implicit conversion done by the compiler to autocast to the target class
	- This might incur some unexpected behavior like:
		- {{embed ((4de926be-81af-4861-a943-27fa6e016bb6))}}
- [What does the explicit keyword mean?](https://stackoverflow.com/questions/121162/what-does-the-explicit-keyword-mean/121163#121163?newreg=5f25e3906543464d9f1ab517a64b2bb0)
	- The compiler is allowed to make one implicit conversion to resolve the parameters to a function. What this means is that the compiler can use constructors callable with a single parameter to convert from one type to another in order to get the right type for a parameter.
	- Here's an example class with a constructor that can be used for implicit conversions:
	- ```c++
	  class Foo
	  {
	  private:
	    int m_foo;
	  
	  public:
	    // single parameter constructor, can be used as an implicit conversion
	    Foo (int foo) : m_foo (foo) {}
	  
	    int GetFoo () { return m_foo; }
	  };
	  ```
	- Here's a simple function that takes a `Foo` object:
	- ```c++
	  void DoBar (Foo foo)
	  {
	    int i = foo.GetFoo ();
	  }
	  ```
	- and here's where the `DoBar` function is called:
	- ```c++
	  int main ()
	  {
	    DoBar (42);
	  }
	  ```
	- The argument is not a `Foo` object, but an `int`. However, there exists a constructor for `Foo` that takes an `int` so this constructor can be used to convert the parameter to the correct type.
	- The compiler is allowed to do this once for each parameter.
	- Prefixing the explicit keyword to the constructor prevents the compiler from using that constructor for implicit conversions. Adding it to the above class will create a compiler error at the function call `DoBar (42)`. It is now necessary to call for conversion explicitly with `DoBar (Foo (42))`
	- The reason you might want to do this is to avoid accidental construction that can hide bugs.
	  Contrived example:
		- You have a `MyString` class with a constructor that constructs a string of the given size. You have a function `print(const MyString&)` (as well as an overload `print (char *string)`), and you call `print(3)` (when you actually intended to call `print("3")`). You expect it to print "3", but it prints an empty string of length 3 instead.
		  id:: 4de926be-81af-4861-a943-27fa6e016bb6