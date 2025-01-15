title:: FeasibleMemoryScheduler::getNonEmptyOpDemandList

- ## What does this function do?
- In general
	- return all buffers of an op that require allocation
- `for (auto& dep : _depsInfo.getOpDeps(opIdx))`
	- 在[[_depsInfo]]中遍历所有的当前`opIdx`需要的dep op
- ```c++
  if (_opOutputTable.find(dep) == _opOutputTable.end()) {
              demandList.insert(dep);
          }
  ```
	- 如果没有在[[_opOutputTable]]中找到dep，也就是没有scheduled，那么需要先执行这个dep
- `else if (_opOutputTable[dep].spilled()) {`
	- 如果这个dep已经被spill掉，遍历需要allocation的buffer，在[[_opWritingToBuffer]]中查找写到buffer的operation。如果这个operation就是dep，那么说明这个operation的输出需要重新allocate，因为已经被spill掉了。