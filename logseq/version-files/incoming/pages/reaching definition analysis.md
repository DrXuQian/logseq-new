- One-line definition
	- What is reaching definition? #card
	  card-last-interval:: 4
	  card-repeats:: 1
	  card-ease-factor:: 2.6
	  card-next-schedule:: 2023-02-01T15:10:31.040Z
	  card-last-reviewed:: 2023-01-28T15:10:31.040Z
	  card-last-score:: 5
		- > a reaching definition for a given instruction is an earlier instruction whose target variable can reach (be assigned to) the given one without an intervening assignment.
- Example
	- `d1` is reaching definition for `d2`
		- ```
		  d1 : y := 3
		  d2 : x := y
		  ```
	- `d1` is no longer reaching definition for `d3` because `d2` break the reach.
		- ```
		  d1 : y := 3
		  d2 : y := 4
		  d3 : x := y
		  ```