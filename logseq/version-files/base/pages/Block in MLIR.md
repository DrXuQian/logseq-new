- ```
  func @example(%arg0: i32) {
    %0 = constant 1 : i32
    %1 = addi %arg0, %0 : i32
    return %1 : i32
  }
  ```
- Block是operation序列的container。通常每个block都有一个唯一的entry。每个block可以包含多个operation。
- Region 是一个或者多个block的集合。通常用于表示control flow。
- 带有多个Block的函数：
	- ```
	  func @conditional(%arg0: i1) -> i32 {
	    %0 = constant 42 : i32
	    cond_br %arg0, ^bb1, ^bb2
	  
	  ^bb1:  // 如果条件为真
	    %1 = constant 1 : i32
	    br ^bb3(%1)
	  
	  ^bb2:  // 如果条件为假
	    %2 = constant 0 : i32
	    br ^bb3(%2)
	  
	  ^bb3(%result: i32):  // 合并块
	    return %result : i32
	  }
	  ```
- 包含Loop的例子：
	- ```
	  func @loop_example() -> i32 {
	    %0 = constant 0 : i32
	    %1 = constant 10 : i32
	    br ^loop(%0)
	  
	  ^loop(%i: i32):
	    %cmp = cmpi "slt", %i, %1 : i32
	    cond_br %cmp, ^loop_body, ^exit
	  
	  ^loop_body:
	    %next_i = addi %i, 1 : i32
	    br ^loop(%next_i)
	  
	  ^exit:
	    return %i : i32
	  }
	  ```
- 嵌套Region:
	- ```
	  func @nested_region_example(%arg0: i32) -> i32 {
	    %0 = constant 0 : i32
	    %1 = constant 10 : i32
	  
	    scf.if %arg0 {
	      %2 = constant 1 : i32
	      %3 = addi %0, %2 : i32
	      %4 = addi %3, %1 : i32
	      %result = select %arg0, %4, %0 : i32
	    } else {
	      %result = constant 0 : i32
	    }
	    return %result : i32
	  }
	  ```