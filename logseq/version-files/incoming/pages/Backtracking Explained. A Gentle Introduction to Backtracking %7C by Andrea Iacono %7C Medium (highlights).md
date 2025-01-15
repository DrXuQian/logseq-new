title:: Backtracking Explained. A Gentle Introduction to Backtracking | by Andrea Iacono | Medium (highlights)
author:: [[Andrea Iacono]]
full-title:: "Backtracking Explained. A Gentle Introduction to Backtracking | by Andrea Iacono | Medium"
category:: #articles
url:: https://medium.com/@andreaiacono/backtracking-explained-7450d6ef9e1a

- Highlights first synced by [[Readwise]] [[Feb 9th, 2023]]
	- Let’s see a pseudo code for traversing this maze and checking if there’s an exit:  function backtrack(junction):      if is_exit:     return true   for each direction of junction:     if backtrack(next_junction):       return true          return false
-