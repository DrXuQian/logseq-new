title:: Backtracking With C++ (highlights)
author:: [[Crack FAANG]]
full-title:: "Backtracking With C++"
category:: #articles
url:: https://medium.com/p/91e3bfc56a21

- Highlights first synced by [[Readwise]] [[Feb 5th, 2023]]
	- It is a refined brute force approach that tries out all the possible solutions and chooses the best possible ones out of them.
	- It is known for solving problems recursively one step at a time and removing those solutions that do not satisfy the problem constraints at any point in time.
	- The term backtracking implies - if the current solution is not suitable, then eliminate that and backtrack (go back) and check for other solutions.
	- The final algorithm can be summarised as:
	  Step 1 − if the current point is a feasible solution, return success
	  Step 2 − else if all paths are exhausted (i.e current point is an endpoint), return failure since we have no feasible solution.
	  Step 3 − else if the current point is not an endpoint, backtrack and explore other points and repeat the above steps.