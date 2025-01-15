---
title: SSA
---
- One line description
	- What is SSA? #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:11:19.060Z
	  card-last-reviewed:: 2023-01-28T15:11:19.061Z
	  card-last-score:: 5
		- > Requires that each variable be assigned exactly once, and every variable be defined before it is used
	- Example
	  id:: 6225c19c-d512-4f44-8ce2-c3dc2f60478b
		- consider this piece of code:
			- ```javascript
			  y := 1
			  y := 2
			  x := y
			  ```
			- We can see that the first line is unnecessary, but a program would have to perform [[reaching definition analysis]] to determine whether the first line is necessary or not.
		- After using SSA format, the result is straightforward.
			- ```javascript
			  y1 := 1
			  y2 := 2
			  x1 := y2
			  ```