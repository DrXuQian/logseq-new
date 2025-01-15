title:: C++: 类后面尖括号

- 模板类
	- src/vpux_compiler/include/vpux/compiler/utils/linear_scan.hpp
	- 类的实例化：
		- `LinearScan<mlir::Value, LinearScanHandler> scan(maxSize.count(), alignment);`
	- 类的定义
		- ```c++
		  template <class LiveRange, class Handler>
		  class LinearScan final {
		  public:
		      using Direction = Partitioner::Direction;
		      using LiveRangeVector = SmallVector<LiveRange>;
		      using LiveRangeIter = typename LiveRangeVector::iterator;
		      using AllocatedAddrs = std::unordered_map<const LiveRange*, std::pair<AddressType, AddressType>>;
		  ...
		  ```