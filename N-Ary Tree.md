*Bin-ary* -> *N-ary* Tree
So Children of Node > 2. 

Going to use NTTP (Non-Type Template Parameter), keeping it a constant at compile-time. 
Since `<int N>` is going to be compile time constant, we can just use `std::array<>`. 

```
#include <array> 
#include <memory> // for smart pointers
#include <iostream>

tempalte <typename T, int N> 
class nAryTreeNode{
	// all a node needs is the value that it holds, and the array holding N children
	
	T value;
	std::array<std::unique_ptr<nAryTreeNode<T, N>>, N> childrenArray;

	nAryTreeNOde(const T& val) : value(val) {} // default childrenArray

	void setChild(int index, std::unique_ptr<nAryTreeNode<T, N>> child)
	{ 
		if(index < 0 || index >= N)
			throw std::out_of_range("Invalid Child Index");

		childrenArray[index] = std::move(child);
	}

	nAryTreeNode<T, N>* getChild(int index) const 
	{ 
		if(index <0 || index >= N)
			throw std::out_of_range("Invalid Child Index");
			
		return children[index].get(); // couldn't move a unique_ptr unless ending this access to the index
	}
};

template <typename T, int N> 
void printTree(const nAryTreeNode<T, N>* node, int depth = 0)
{ 
	if(!node) return; 
	// in order traversal
	for(int i = 0; i < depth; ++i) std::cout << " ";
	std::cout << node->value << "\n";
	for(const auto& child : node->children)
		printTree(child.get(), depth + 1);
}
```
`printTree` -> the `for(const auto& child ...` is very interesting. 




