- Can only use # rather than """""", or ''''''
- If use the latter one, the comment will be treated like string:
	- ```python
	  my_dict = {
	      "key1": "value1",
	      """This is not a comment.
	      It's a multi-line string.""": "value2",
	      "key3": "value3"
	  }
	  
	  print(my_dict)
	  
	  # get {'key1': 'value1', 'This is not a comment.\nIt's a multi-line string.': 'value2', 'key3': 'value3'}
	  
	  ```