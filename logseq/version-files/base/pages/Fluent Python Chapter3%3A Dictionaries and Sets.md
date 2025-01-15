title:: Fluent Python Chapter3: Dictionaries and Sets
- ## Modern dict Syntax
- ### dict Comprehensions
	- `dict` can be build using `dict` comprehension just like `list` comprehension
	- `dict` can be created by directly applying `{}` around `tuple`.
	- What would the following code generate? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-04-28T12:40:45.495Z
	  card-last-reviewed:: 2024-04-24T09:40:45.496Z
	  card-last-score:: 5
	  ```
	  >>> a = [('a', 2), ('b', 3)]
	  >>> d = dict(a)
	  >>> d
	  ```
		- ```
		  {'a': 2, 'b': 3}
		  ```
- ### Unpacking Mappings
	- What symbol is used for iterable unpacking and what symbol is used for dictionary unpacking?  #card
		- `*` used for iterable and `**` used for dict.
	- What does the following command print? #card
	  ```python
	  >>> print(*[1], *[2], 3)
	  >>> dict(**{'x': 1}, y=2, **{'z': 3})
	  ```
		- ```python
		  1, 2, 3
		  {'x': 1, 'y': 2, 'z': 3}
		  ```
	- Can we use `*` to unpack iterator? #card
		- Yes, we can.
	- What will happen for the following line? #card
	  ```python
	  >>> *range(4)
	  ```
		- ```python
		  >>> *range(4)
		    File "<stdin>", line 1
		  SyntaxError: can't use starred expression here
		  >>> *range(4),
		  (0, 1, 2, 3)
		  ```
		- And this is because adding `,` would convert _ into tuple and that is equivalent to:
			- `tuple(*range(4), )`
	- What will happen for the following line? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:11:17.842Z
	  card-last-reviewed:: 2024-02-23T02:11:17.842Z
	  card-last-score:: 5
	  ```python
	  >>> **{'a':1}
	  ```
		- ```python
		    File "<stdin>", line 1
		      **{'a':1}
		      ^
		  SyntaxError: invalid syntax
		  >>> **{'a':1},
		    File "<stdin>", line 1
		      **{'a':1},
		      ^
		  SyntaxError: invalid syntax
		  >>> {**{'a':1}}
		  {'a': 1}
		  >>>
		  ```
	- What is the value for `x` in this case? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:12:04.435Z
	  card-last-reviewed:: 2024-02-23T02:12:04.435Z
	  card-last-score:: 5
	  ```
	  >>> {'a': 0, **{'x': 1}, 'y': 2, **{'z': 3, 'x': 4}}
	  ```
		- ```
		  {'a': 0, 'x': 4, 'y': 2, 'z': 3}
		  ```
		- In this case, duplicate keys are allowed. Later occurrences overwrite previous ones—see the value mapped to `x` in the example.
	- What is the constraint of using `**` for argument in function call? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:11:02.345Z
	  card-last-reviewed:: 2024-02-23T02:11:02.346Z
	  card-last-score:: 5
	  ```
	  >>> def dump(**kwargs):
	  ...     return kwargs
	  ...
	  >>> dump(**{'x': 1}, y=2, **{'z': 3})
	  {'x': 1, 'y': 2, 'z': 3}
	  ```
		- This works when keys are all strings and unique across all arguments (because duplicate keyword arguments are forbidden):
	- Can we write `func(**{'a': 2}, [1, 2, 3], c=4)`? #card
		- That will raise error:
			- ![image.png](../assets/image_1675688806321_0.png)
	- What will the following print? #card
	  ![image.png](../assets/image_1675688866241_0.png)
		- another arg through *args: [1, 2, 3]
		  c = 4
		  a = 2
- ### *Merging Mappings with |
	- * this is introduced in python3.9
- ## *Pattern Matching with Mappings
- ## Standard API of Mapping Types
	- The `collections.abc` module provides the `Mapping` and `MutableMapping` ABCs describing the interfaces of `dict` and similar types.
		- ![image.png](../assets/image_1675689702984_0.png){:height 206, :width 659}
	- Why is using the following code to check variable type better than checking if that is a concrete `dict` type? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:11:06.215Z
	  card-last-reviewed:: 2024-02-23T02:11:06.216Z
	  card-last-score:: 5
	  ```python
	  >>> my_dict = {}
	  >>> isinstance(my_dict, abc.Mapping)
	  True
	  >>> isinstance(my_dict, abc.MutableMapping)
	  True
	  ```
		- Using isinstance with an ABC is often better than checking whether a function argument is of the concrete dict type, because then alternative mapping types can be used.
- ### What Is Hashable
	- What is a hashable object? What method does the object need? #card
		- > An object is hashable if it has a hash code which never changes during its lifetime (it needs a `__hash__()` method), and can be compared to other objects (it needs an `__eq__()` method). Hashable objects which compare equal must have the same hash code.[2](https://learning.oreilly.com/library/view/fluent-python-2nd/9781492056348/ch03.html#idm46582504518496)
	- Numeric types and flat immutable types `str` and `bytes` are all hashable. Container types are hashable if they are immutable and all contained objects are also hashable. A `frozenset` is always hashable, because every element it contains must be hashable by definition. A `tuple` is hashable only if all its items are hashable.
	- Which ones of these are hashable components? #card
	  ```python
	  >>> tt = (1, 2, (30, 40))
	  >>> hash(tt)
	  
	  >>> tl = (1, 2, [30, 40])
	  >>> hash(tl)
	  
	  >>> tf = (1, 2, frozenset([30, 40]))
	  >>> hash(tf)
	  ```
		- ```
		  8027212646858338501
		  
		  Traceback (most recent call last):
		    File "<stdin>", line 1, in <module>
		  TypeError: unhashable type: 'list'
		  
		  -4118419923444501110
		  ```
	- [[Frozen set]]
		- What is a python frozen set and what will the following code generate? #card
		  collapsed:: true
		  ```python
		  # tuple of vowels
		  vowels = ('a', 'e', 'i', 'o', 'u')
		  
		  fSet = frozenset(vowels)
		  print('The frozen set is:', fSet)
		  print('The empty frozen set is:', frozenset())
		  
		  # frozensets are immutable
		  fSet.add('v')
		  ```
			- The `frozenset()` function returns an immutable `frozenset` object initialized with elements from the given `iterable`.
			- Syntax: `frozenset([iterable])`
			- ```
			  The frozen set is: frozenset({'a', 'o', 'u', 'i', 'e'})
			  The empty frozen set is: frozenset()
			  Traceback (most recent call last):
			    File "<string>, line 8, in <module>
			      fSet.add('v')
			  AttributeError: 'frozenset' object has no attribute 'add'
			  ```
		- What will happen if we apply frozen set to a dictionary? #card
		  collapsed:: true
		  ```python
		  # random dictionary
		  person = {"name": "John", "age": 23, "sex": "male"}
		  
		  fSet = frozenset(person)
		  print('The frozen set is:', fSet)
		  ```
			- ```
			  The frozen set is: frozenset({'name', 'sex', 'age'})
			  ```
	- What dtypes are included in python container type? #card
		- `list`, `tuple`, `dict`, `set`
	- When will the user defined types hashable? #card
	  ```python
	  class test0_for_hash:
	      self.test_param = ['a', 'b']
	  o = test0_for_hash()
	  print(hash(o))
	  
	  class test1_for_hash:
	      self.test_param = ['a', 'b']
	      def __eq__(self, other):
	          return self.test_param == other.test_param
	  o = test1_for_hash()
	  print(hash(o))
	  
	  class test_for_hash:
	      test_param = ('a', 'b')
	  
	      def __eq__(self, other):
	          return self.test_param == other.test_param
	  
	      def __hash__(self):
	          return hash(self.test_param)
	  
	  o = test_for_hash()
	  print(hash(o))
	  ```
		- User-defined types are hashable by default because their hash code is their `id()` , and the `__eq__()` method inherited from the `object` class simply compares the object IDs. If an object implements a custom `__eq__()` that takes into account its internal state, it will be hashable only if its `__hash__()` always returns the same hash code. In practice, this requires that `__eq__()` and `__hash__()` only take into account instance attributes that never change during the life of the object.
		- So for the example, first and last will be hashable.
- ### Overview of Common Mapping Methods
	- What is the difference bw `dict` and `defaultdict` and `OrderedDict`? #card
	  collapsed:: true
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:11:09.875Z
	  card-last-reviewed:: 2024-02-23T02:11:09.875Z
	  card-last-score:: 5
		- `OrderedDict` and `defaultdict` are both subclasses of `dict` . They have different uses.
		- `OrderedDict` keeps the elements in the order that they were in when they were first inserted.
			- >If we remove an element and re-add it, the element will be pushed to the back. If an element’s key changes, the position does not change.
		- A `defaultdict` is a useful subclass that doesn’t raise a `KeyError` exception when trying to access an undefined key.
	- What is the output of the following code for `OrderedDict`? #card
	  collapsed:: true
	  ```python
	  from collections import OrderedDict
	  
	  mydict = OrderedDict([('a', '1'), ('b', '2'), ('c', '3')])
	  
	  for k in mydict.items():
	    print(k)
	  print('\n')
	  mydict.pop('b')
	  mydict['b'] = '2'
	  for k in mydict.items():
	    print(k)
	  
	  ```
		- ```
		  ('a', '1')
		  ('b', '2')
		  ('c', '3')
		  
		  
		  ('a', '1')
		  ('c', '3')
		  ('b', '2')
		  ```
	- What is the output of the following code for `defaultdict`? #card
	  collapsed:: true
	  ```python
	  from collections import defaultdict
	  
	  def defval():
	    return 'default value'
	  
	  mydict = defaultdict(defval)
	  mydict['a'] = 1
	  mydict['b'] = 2
	  mydict['c'] = 3
	  
	  for k in mydict.items():
	    print(k)
	  
	  print('\n') 
	  
	  # if we try to get 'd' 
	  print(mydict['d']) 
	  # with a 'generic' dict this will raise a KeyError exception
	  
	  print('\n') 
	  
	  # it also add it to the dict
	  for k in mydict.items():
	    print(k)
	  ```
		- ```
		  ('c', 3)
		  ('a', 1)
		  ('b', 2)
		  
		  
		  default value
		  
		  
		  ('c', 3)
		  ('d', 'defaul
		  ```
- How does `d.update(m)` work for dictionary in python? #card
	- The way `d.update(m)` handles its first argument `m` is a prime example of *duck typing*: it first checks whether `m` has a `keys` method and, if it does, assumes it is a mapping. Otherwise, `update()` falls back to iterating over `m`, assuming its items are `(key, value)` pairs.
	- Duck typing: **Duck typing** in computer programming is an application of the [duck test](https://en.wikipedia.org/wiki/Duck_test)—"If it walks like a duck and it quacks like a duck, then it must be a duck"—to determine whether an [object](https://en.wikipedia.org/wiki/Object_(computer_science)) can be used for a particular purpose.
- ### Inserting or Updating Mutable Values
- What is a method to get `d[k]` when `d` is a `dict` and avoid fail-fast when `k` is not in `d.keys()`? #card
	- `d.get(k, default)`
- What is `sys.argv` and how to use this in python? #card
  ```python
  # python test_sys_argv.py 1 2 3 4
  import sys
    
  print("This is the name of the program:", sys.argv[0])
    
  print("Argument List:", str(sys.argv))
  ```
	- [Command line arguments](https://www.geeksforgeeks.org/python-set-6-arguments/) are those values that are passed during calling of program along with the calling statement. Thus, the first element of the array `sys.argv()` is the name of the program itself. `sys.argv()` is an array for [command line arguments](https://www.geeksforgeeks.org/python-set-6-arguments/) in Python.
	- So the upper code would output:
	- ```
	  (base) qianxu@xuqian-mobl1:~/Software$ python test_sys_argv.py 1 2 3 4
	  This is the name of the program: test_sys_argv.py
	  Argument List: ['test_sys_argv.py', '1', '2', '3', '4']
	  ```
- What does the following line do? #card
  ```python
  my_dict.setdefault(key, []).append(new_value)
  ```
  Is it more efficient than running:
  ```python
  if key not in my_dict:
      my_dict[key] = []
  my_dict[key].append(new_value)
  ```
	- The latter code performs at least two searches for `key`—three if it’s not found—while `setdefault` does it all with a single lookup.
	- The difference with using `defaultdict` is that `defaultdict` takes care of every look up, not just when inserting `value` for some certain `key`.
- ## Automatic Handling of Missing Keys
- ### defaultdict: Another Take on Missing Keys
	- What is the `defaultdict` way of translating the following code #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-03-02T15:02:40.196Z
	  card-last-reviewed:: 2024-02-27T12:02:40.196Z
	  card-last-score:: 5
	  :LOGBOOK:
	  CLOCK: [2023-02-15 Wed 21:30:33]--[2023-02-15 Wed 21:30:34] =>  00:00:01
	  :END:
	  ```python
	  # 1
	  my_dict.setdefault(key, []).append(new_value)
	  # 2
	  if key not in my_dict:
	      my_dict[key] = []
	  my_dict[key].append(new_value)
	  ```
		- ```python
		  index = collections.defaultdict(list)
		  index[word].append(location) 
		  ```
	- What will happen for the following call for `defaultdict`? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:12:01.303Z
	  card-last-reviewed:: 2024-02-23T02:12:01.303Z
	  card-last-score:: 5
	  ```python
	  index = collections.defaultdict(list)
	  print(index.get('some_key'))
	  print('some_key' in index.keys())
	  ```
		- ```python
		  >>> import collections
		  >>> index = collections.defaultdict(list)
		  >>> index.get('some_key') is None
		  True
		  >>> print('some_key' in index.keys())
		  False
		  ```
- ### The `__missing__` Method
	- Underlying the way mappings deal with missing keys is the aptly named `__missing__` method. This method is not defined in the base `dict` class, but `dict` is aware of it: if you subclass `dict` and provide a `__missing__` method, the standard `dict.__getitem__` will call it whenever a key is not found, instead of raising `KeyError`.
	- What will happen if you have a `__missing__` method defined for a subclass of `dict`? #card
	  ```python
	  class StrKeyDict0(dict):
	  
	      def __missing__(self, key):
	          if isinstance(key, str): 
	              raise KeyError(key)
	          return self[str(key)] 
	  
	  >>> d = StrKeyDict0([('2', 'two'), ('4', 'four')])
	  >>> d['2']
	  >>> d[4]
	  >>> d[1]
	  ```
		- ```
		  >>> d['2']
		  'two'
		  >>> d[4]
		  'four'
		  >>> d[1]
		  Traceback (most recent call last):
		  ...
		  KeyError: '1'
		  ```
	- Why is using `collections.UserDict` better than `dict` to define a user-defined mapping type?
	- What is the output on the console for this test of `dict`? #card
	  card-last-interval:: 4.14
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2024-02-27T05:11:08.108Z
	  card-last-reviewed:: 2024-02-23T02:11:08.109Z
	  card-last-score:: 5
	  ```python
	  >>> k = {'a': 2, 'b': 3}
	  >>> 'a' in k
	  ```
		- ```
		  True
		  ```
	- What does the following code do with user defined dict subclass? #card
	  ```python
	  class StrKeyDict0(dict):  
	  
	      def __missing__(self, key):
	          if isinstance(key, str):  
	              raise KeyError(key)
	          return self[str(key)]  
	  
	      def get(self, key, default=None):
	          try:
	              return self[key]  
	          except KeyError:
	              return default  
	  
	      def __contains__(self, key):
	          return key in self.keys() or str(key) in self.keys()  
	  
	  >>> d = StrKeyDict0([('2', 'two'), ('4', 'four')])
	  
	  >>> d.get('2')
	  >>> d.get(4)
	  >>> d.get(1, 'N/A')
	  
	  >>> 2 in d
	  >>> 1 in d
	  ```
		- ```python
		  >>> d.get('2')
		  >>> 'two'
		  >>> d.get(4)
		  >>> 'four'
		  >>> d.get(1, 'N/A')
		  >>> 'N/A'
		  >>> 2 in d
		  >>> True
		  >>> 1 in d
		  >>> False
		  ```
-