Unlike C-style arrays, there are lots of benefits that we have with `std::array`. 
Static memory allocation and constant-time access, but we also get the ranged based for-loops and member functions for size checking. 

**Static**. 

### Purpose / Suitability
- Predictability, fixed size. 
- Stack allocation, swift access and minimal overhead.
If we need to know the size of the array at compile-time. 

If we are going to use fixed size collections, `std::array` is the go to choice. 

Performance critical - no dynamic memory allocation.

Safe array manipulations - `at()` member function, safer compared to C-style arrays, especially in thwarting off out of bounds access. 

When we pass C-style arrays to functions, we lose the size of the array, therefore we have to pass it separately to the function, as a `std::size_t` or just an `int` etc. 

I believe that `std::array` is one of the few that stack allocates, rather than heap allocates. 

Obviously if we needed dynamic sizing, then  we would use `std::vector`. 

### Performance
Insertion and deletion don't count for obvious reasons. 

Access : `O(1)` for any position, due to index, and offsetting base address. 

Memory : given the stack allocation, very good indeed and very fast. 

Quite thread safe, however, if we are going to write to the same index, this will demand synchronization. 

`std::sort` is going to be `O(n log n)`. 

`std::binary_search()` is going to be `O(log n)`. 

`fill()` - set all elements to a value

`swap()` - swap contents with another array of the same type and size (very important). 

Plays nicely due to the random access capabilities. 

`at()` can throw exceptions, primarily `std::out_of_range`. 

Some points : 
- Utilising that fixed size idea
- Structured bindings to destructure elements. 
- Compile time calculations due to `constexpr` nature
- `std::sort` `std::find` 



```
#include <algorithm> 
#include <array> 
#include <iostream> 

struct Point{ 
	int x{0}, y{0};
};

// consteval - only at compile time
// constexpr - compile time if possible, otherwise, do at run time
constexpr int sumArray(const std::array<int, 5> &arr) // if can be done at compile time do
{ 
	int sum = 0;
	for(const auto& val : arr) { sum += val; }
	return sum;
}

int main() 
{ 
	std::array<int, 5> numbers = {5, 3, 8, 1, 4};
	std::array<Point, 3> points = {{{1, 2,}, {3, 4}, {5, 6}}};
	// structured bindings
	
	for(const auto&[x, y] : points)
	{ 
		std::cout << "(" << x << ", " << y << ")\n";
	}

	constexpr std::array<int, 5> constNumber = {1, 2, 3, 4, 5};
	
	constexpr int totalSum = sumArray(constNumbers);

	std::cout << "\nCompile time sum of array elements: " << totalSum << "\n";

	std::sort(numbers.being(), numbers.end());

	std::cout << "\nSorted Numbers: ";
	for(const auto& num : numbers)
	{ 
		std::cout << num << " ";
	}

	int searchFor = 3; 
	
	if(std::find(numbers.begin(), numbers.end(), searchFor) != numbers.end())
	{ 
		std::cout << "\nFound " << searchFor << "\n";
	}
}
```

![[Pasted image 20250711142012.png]]
Points already mentioned, but really well layed out here. 

