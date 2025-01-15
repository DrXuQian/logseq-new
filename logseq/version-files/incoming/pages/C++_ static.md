---
title: C++: static
---

- {{youtube https //www.youtube.com/watch?v=f3FVU-iwNuA&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=22}}

- static outside of a class or struct
	 - only valid for that translation unit where the static variable or function is defined.

	 - try declare your functions or variables static unless you want them to be linked elsewhere

	 - static functions or variables can be useful in header files where two cpp files include that same header file, it will create two private static variable for these two cpp files.

- {{youtube https //www.youtube.com/watch?v=V-BFlMrBtqQ&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=23}}

- static for classes and structs[[classes and structs]]
	 - static variable meaning there is going to be only one variable across all instances of the class

	 - if one instance changes the static variable, the change will apply to all instances

	 - #TODO why need to define static class members outside of the class?

	 - a static method (function) don't have the class reference

- {{youtube https //www.youtube.com/watch?v=f7mtWD9GdJ4&list=PLlrATfBNZ98dudnM48yfGUldqGD0S4FFb&index=24}}

- static in a local scope
	 - lifetime of our entire program but scope with in a local scope

	 - location of a static variable #TODO
