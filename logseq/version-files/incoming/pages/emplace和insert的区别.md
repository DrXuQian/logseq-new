---
title: emplace和insert的区别
---

- https://www.youtube.com/watch?v=SPN2UiLFyt4
	 - all slow

	 - try_emplace...

- https://stackoverflow.com/questions/26446352/what-is-the-difference-between-unordered-mapemplace-and-unordered-mapinsert

- 

- > The advantage of emplace is, it does in-place insertion and avoids an unnecessary copy of object. For primitive data types, it does not matter which one we use. But for objects, use of emplace() is preferred for efficiency reasons.
