- What is `VPU::NCEOpInterface` and `VPU::ClusteredOpInterface` ?
- What is `TilingInfoOpInterface`?
	- This is defined to represent the tiling related information of a given operator.
	- The `::mlir` is the scope resolution operator in C++, refer to the `mlir` in global namespace.
	- ```c++
	  class TilingInfoOpInterface : public ::mlir::OpInterface<TilingInfoOpInterface, detail::TilingInfoOpInterfaceInterfaceTraits> {
	  public:
	    using ::mlir::OpInterface<TilingInfoOpInterface, detail::TilingInfoOpInterfaceInterfaceTraits>::OpInterface;
	    template <typename ConcreteOp>
	    struct Trait : public detail::TilingInfoOpInterfaceTrait<ConcreteOp> {};
	    /// Check, if the provided tiling configuration is supported by the operation implementation
	    bool isSupportedTiling(const vpux::OutputTiling& tiles, vpux::TilingMode tilingMode, vpux::Logger log);
	  };
	  ```
- What is a scope resolution operator `::`? #card
	- **Example 1: Accessing a global variable**
	- ```c++
	  #include 
	  int x = 20; // Global variable
	  int main() {
	    int x = 10; // Local variable
	    std::cout << "Local x: " << x << std::endl;
	    std::cout << "Global x: " << ::x << std::endl; // Accessing global 'x'
	    return 0;
	  }
	  Output:
	  Local x: 10
	  Global x: 20
	  ```
	- **Example 2: Accessing class members**
	- ```c++
	  #include 
	  class MyClass {
	  public:
	    int num;
	    void printNum() {
	        std::cout << "Class member num: " << num << std::endl;
	    }
	  };
	  int main() {
	    MyClass obj;
	    obj.num = 42;
	    obj.printNum();
	    return 0;
	  }
	  Output:
	  Class member num: 42
	  ```