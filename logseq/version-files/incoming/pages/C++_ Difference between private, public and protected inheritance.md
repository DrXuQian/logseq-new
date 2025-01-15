title:: C++: Difference between private, public and protected inheritance

- What is the difference between public and private inheritance in C++? #card
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.6
  card-next-schedule:: 2023-02-01T15:13:53.841Z
  card-last-reviewed:: 2023-01-28T15:13:53.841Z
  card-last-score:: 5
	- 1. private inheritance is the default method in C++. The public and protected params in base class become private members in inherited class.
	  public inheritance keep the public and private property in the original base class.
	- 2. private can not convert to the base class while public inheritance can.
- [Difference between private, public, and protected inheritance](https://stackoverflow.com/questions/860339/difference-between-private-public-and-protected-inheritance/1372858#1372858)
	- **IMPORTANT NOTE**: `Classes B`, `C` and `D` all contain the variables x, y and z. It is just question of access.
	- ```c++
	  class A 
	  {
	      public:
	         int x;
	      protected:
	         int y;
	      private:
	         int z;
	  };
	  
	  class B : public A
	  {
	      // x is public
	      // y is protected
	      // z is not accessible from B
	  };
	  
	  class C : protected A
	  {
	      // x is protected
	      // y is protected
	      // z is not accessible from C
	  };
	  
	  class D : private A    // 'private' is default for classes
	  {
	      // x is private
	      // y is private
	      // z is not accessible from D
	  };
	  ```