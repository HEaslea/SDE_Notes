## `std::array`
Static memory allocation just like `int numbers[5]`. 
Super good for performance - when we know how large our dataset is. 
Stack allocation, offering swift access and minimal overhead. 

As soon as we know the size of the data, the array is the right option. 

Performance critical, as only on the stack. 

Offer bound-checking `at()` -  so that we can check without worrying. 

When we pass C style arrays, it degrades to a pointer, meaning that we lose the idea of the size, however, array carries it with it. 

Contiguous memory, cache friendly. 

Predictable memory patterns - No surprise memory allocations, great for embedded systems and real time simulations

**Performance**: 
Access : Ram so `O(1)`
Memory Overhead : minimal given the stack, no heap overhead.
Fixed-size efficiency is at the cost of static sizing. 

**Thread safety**: Writing concurrently to the same element demands synchronization. 

`std::sort` - `O(n log n)` - introsort - insertion for small - heapsort if we feel that quicksort depth is too high. 
Search `O(log n)` with a sorted array through `std::binary_search()`. 

`fill()` sets all elements to a value
`swap()` swaps contents with another array of the same type and size

`std::out_of_range` with out of range `at()`

`constexpr` nature - meaning that we can do compile-time calculations. 

Structured Bindings from C++17, simplifying accessing multiple elements returned from a function or decomposing the contents of complex data structures. 
```
struct Point 
{ 
	int x{0}, y{0};
};

constexpr int sumArray(coinst std::array<int, 5>& arr)
{ 
	int sum = 0; 
	for(const auto& val : arr) { sum += val; }
	return sum;
}

int main() 
{ 
	std::array<int, 5> numbers = {5, 3, 8, 1, 4};
	std::array<Point, 3> points = {{{1, 2}, {3, 4}, {5, 6}}};

	// some structured bindings
	for(const auto &[x, y] : points)
	{ 
		std::cout << "(" << x << ", " << y << ")\n";
	}

	constexpr std::array<int, 5> constNumbers = {1, 2, 3, 4, 5};

	// will occur at compile time
	constexpr int totalSum = sumArray(constNumbers);

	std::sort(numbers.begin(), numbers.end());

	for(const auto& num : numbers)
		std::cout << num << " ";
		
	int searchFor = 3;

		if(std::find(numbers.begin(), numbers.end(), searchFor) != numbers.end()) // if found
		{ 
			// if found
		} else { 
			// not found
		}
	return 0;
}
```

`std::array` has more functionality than `C-style arrays`. 

`[]` is good, but `at()` is much better, and safer, guarding against out of bounds. 

Size clarity is crucial. 

## `std::vector`
Dynamic in resize: random access, insertions or deletions particularly at the end. Cache locality is essential. 

If we need to grow and shrink an array. 
Random access. 
Sequential data storage - contiguous memory. 

`std::back_inserter` which obviously won't work for `std::array`. 
There is some memory overhead - using the heap - then the control block - T* begin, T* end, T* cap (pointer to the end of the allocated storage). 

Frequent back inserters is great as vector is super good at that. 

Cache-friendly iteration. 

Good for compactness and portability - serializing over a network. 

Polymorphic Data Storage : pointers and references to object. 

Not great when we need to insert and delete in the middle a whole lot. 
Use `reserve()` in order to pre-allocate memory. 

Insertion `O(1)` at the beginning and the end  - `O(n)` everywhere else - as we have to move a lot of stuff. 

Delete `O(1)` at the end - `O(n)` everywhere else again. 

Access `O(1)` - swift af, random access. 

Some trade-offs `O(1)` can be countered by `O(n)`. 

Allocators can work really well for this. 

Would need some thread safety synchronizations. 
`std::deque` fast insertions at both ends - double ended queue. 
`std::list` - optimizing middle insertions and deletions. 

Same sorting as arrays really

`emplace_back()` construct elements directly in place, rather than moving.
`shrink_to_fit()` just don't do it prematurely. 

Random access is the one. 

Preserves state even if exceptions thrown. 

Customization of allocation meaning that we have better strageties. 

`std::vector<bool>` is a controversial specialization in C++. 
One bit per boolean value, saving some space there. 
Still use it if you want, but use caution. 

```
std::vector<bool> bv = {true, false, true, true, false};

bv[1] = true;

bv.clear();
```

`std::vector<bool>` memory savings by storing booleans as individual bits. 

## `std::deque`
Flexibility comes at the cost of a slightly more flexible structure. 
Rapid insertions and deletions at both ends. 
`O(1)` at both ends. 
Dynamic size with no need for manual memory management. 
Good cache performance for front and back operations. 

Chunked dynamic array - list of fixed-size arrays. 
Multiple fixed size memory blocks (chunks) meaning that we can load a lot at the same time for cache. 
Central control structure - `std::map`. Pointer in these maps to a fixed size array often (512 or 1024 bytes, implementation dependent). 

Number of elements is usually a power of 2, for efficiency. 

New blocks can be created dynamically. 
`push_front()`  and `push_back()`  are both efficient. 
Inserting at the front this time is `O(1)` not `O(n)` like in vector. 

Good for random access - which seems odd. 
Using `[]` or `at()`. 

First it would need to get which block that we are looking into. 
Which is very easy when we have a consistent block size. 
$\text{block index} = \frac{\text{element index}}{\text{block size}}$

Then we use the map in order to get to the right block. 
Then we look in the block in order to get to the right point in the block, meaning that we are given random access for pointer mathematics. 

Fast insertion at both ends . Double ended queue. 
Great for implementing a stack and a queue (FIFO and LIFO). 
Good in this regard, unlike other containers that are only really good for one or the other. 

Random access - Random Access - Random Access. 

Dynamic growth in both directions - not like other containers. 

Buffering data - building applications that go from one end to the other, not really caring about the stuff in the middle - that's when we use `std::deque`. 

Sliding window algs - where elements are continuously added and removed  - that's when `std::deque` really shines. 

Adaptable to other data structures - not 100% sure what that means, however, there it is. 

Slightly more overhead per elements than other containers. 
The array of pointers (could be a `std::map` ) in order to get to the blocks. (8 bytes per pointer depending on the system, 64 bit (8 bytes)). 
N blocks - therefore N pointers. 

![[Pasted image 20250224151439.png]]

undo / redo mechanisms with a limit. Great for this and then we can just put a limit on it with a wrapper. 

Insertions - `O(1)` for front and back, `O(n)` in the middle. 
Same for deletions
Access `O(1)` for random access. 

Slightly higher memory overhead than vector. 

There is no doubling of size - unlike vector - meaning less memory wasted. 

Thread safe - but we need the usual mechanism to make concurrent writes and reads safe. 

Between vector and list - terms of performance and mem characteristics. 

`O(n log n)` sort - introsort
Searching - `O(n)` unsorted - `O(log n)` for binary search `std::binary_search()`. 

`emplace_front()` or `emplace_back()` 
`resize()`

Iterating over elements is worse than `vector` - not all contiguous memory. 

Random access -therefore can leverage STL algorithms. 
 
 **Best Practices**: 
 Non completely contiguous - there are sometimes less predictable due to this. 

If middle operations are found to be a bottleneck, then we need to move to something else like `std::lsit` or `std::vector`. 

non contiguous nature - may be an issue if we have API that require raw arrays.

In place creation with `emplace_front` and `emplace_back`. 

## `std::list`
Double linked list. 
Nothing about this is contiguous. 
Gaining immense flexibility but losing the contiguous nature of the data. 

Insertions and deletions are only `O(1)` if we have an iterator to that point. 
No random access - access time will have to be `O(n)`. 
So only use when we don't need direct access, just middle and ends for insertion and deletion. 

Constant time insertions, and deletions - given that we are pointing to them, no other elements need to move. 

No faster searching and cache friendliness. 

Iterator validity preservation is vital. 

Really really good if we have inherently cache-unfriendly daata in each node. 

`std::forward_list` just forward linked list of course. 
`std::list` is bidirectional, which is great for some algorithms. 

Provided that we have an iterator to that position, insertion and deletion will be constant time. 

however, we do have to find that node that we want to insert before or after in of itself, meaning that might outweigh the benefit. 

Complex data movement without actually moving all that data - just point in a different order- like reversing a linked list - you don't move the nodes in memory, just move the pointers and the implementation is the same. 

While `std::queue` is a great choice, we can use `std::list` just with slightly different implementation on top of that. 

Great for undo / redo function. 

LRU - least recently used  cache implementation - efficiency in moving elements around without actually changing their memory at their core. 

This can be implemented so that the last link can circle back to the first - circular lists. 

Graphs need adjacency lists - therefore this is a great candidate for that. 

Insertion and deletion for known positions given that we have an iterator is `O(1)` meaning we don't have to move anything else
`O(n)` access - as we have to go through the list. 

There can be cache misses, increased memory overhead. `std::vector` will outperform `std::list` for most cases. 

Each allocation is independent, therefore when we have an allocator that will just be for that specific node. 

Sorting - `O(n log n)` `std::list::sort()`
Searching - `O(n)` - `std::find()` due  to lack of random access. 

`emplace_front/ emplace_back`
`splice` super easy tom make one list from another. 
`merge` combines two sorted lists;
`unique` will remove duplicate elements. 

The cost of finding the insertion point, actually outweighs the adding of that element when we get there. 
So still not as great as `std::vector` or `std::list`, they are roughly similar in trade-offs. 

`std::list` will still work with STL algs, however, it's not ideal, such as `std::random_shuffle`. 

The general idea is that we are not going to use this unless we have profiled our code and found that `std::vector` isn't as good as `std::list`> 

Using `std::list::sort` is essential - rather than `std::sort` - C requires random access iterators - which `std::list` doesn't support.  

Bidirectional does mean that we have that memory overhead too. 

Use iterators for powerful insertions and deletions that are `O(1)` rather than `O(n)` like in vectors even when we are on the place to insert and delete. 

Constant time moving and transfer of elements between lists in constant time using `splice`. 

Direct construction with `emplace_front and emplace_back`. 

Two pointers per element for the memory overhead. 

## `std::forward_list`
Only pointing to the next node. 
Reducing memory overhead every so slightly. 
We lose bidirectional. 
Save on memory overhead if we don't need to go back. 

Efficient insertions and deletions at any location in the list - provided that we are pointing to the right node. 
Using less memory than `std::list`.

Constant time insertions or deletions irrespective of the position. 
Memory overhead of the bidirectional not needed. 

Fast insertions at the front - optimal efficiency here. 

Imagine that we don't want that bidirectional nature of `std::list` or that would break what we are doing, somehow, so then `std::forward_list` is the one. 

Good for stack implementation due to fast front insert and deletion. 

Good for adjacency lists again. 

Some garbage collection algs do use this. 

Minimal overhead due to only one pointer. 

Same performance of `std::list` really. 

`emplace_front`
`remove` - remove by a value
`splice_after` transferring elements from one list to another. 

Please remember that we have NO `size()` or `push_back()` for this. 

Example: 
```
tempalte <typename KeyType, typename ValueType> 
class ChainedHashTable{
	struct Bucket 
	{ 
		KeyType key; 
		ValueType value; 
		Bucket(KeyType k, ValueType v) : key(k), value(v)  {}
	};
	
	std:;vector<std::forward_list<Bucket>> table;
	size_t capacity; 
	size_t hash(const KeyType& key) const 
	{ 
		return std::hash<KeyType>{}(key) & capacity;
	}
public: 
	ChainedHashTable(size_t capacity) : capacity(capacity)
	{ 
		table.resize(capacity);
	}

	bool get(const KeyType & key, valueType & value) const 
	{ 
		const auto& list = table[hash(key)];
		for(const auto& bucket : list)
		{ 
			if(bucket.key == key)
			{ 
				balue = bucket.value;
				return true;
			}
		}
		return false;
	}

	void put(const KeyType& key, const ValueType& value)
	{ 
		auto& list = table[hash(key)]; // get the right list
		// then we go through that bucket's list
		for(auto& bucket : list){ 
			if(bucket.key == key)
			{ 
				bucket.value = value;
				return;
			}
		}
		list.emplace_front(key, value); // just put at the front for `O(1)`
	}

	bool remove(const KeyType& key)
	{ 
		auto& list = table[hash(key)]; // get the right list
		return list.remove_if([&](const Bucket & bucket) 
		{ 
			return bucket.key == key;
		});
	}
};
```
```
if(!hashTable.get("banana", value))
{ 
	// do if we don't ahve that in the hash table
}
```

Remember that we should be preferring `std::vector` mostly. 

Lacking direct access - therefore access is `O(n)`> 

No random access - forward iteration only. 

Good additions and in place construction. 

Recheck iterator validity after modifications, post insertions or deletions, as they may become invalidated. 

## `std::string`
Managing sequences of chars. 
Though it's not classified as a container, it certainly acts like one. 
Categorized in its own `string` category. 
Special container for chars. 

Specialization of `std::vector<char>`. 
Manipulating textual data. 
String input and string output. 
Dynamic text modification is frequent, when we ened to access individual chars. 

Manipulating textual data . 
InteractingString input and string output . 
Dynamic text modification is frequent  , when we need to access individual chars.

String views - `std::string_view`. 

Text processing - parsing files - processing logs - dynamic text manipulation
User input and output  - accepting user inputs - producing human readable outputs
Data Serialization - Encoding data as a string for transport/ storage

Like vector `O(1)` insertion at the end `O(n)` for elsewhere. 
String has custom memory layout optimized for these operations. 

SSO - small string optimization - short strings < 15 chars. 

`O(n)` for deletion, as the others might need moving. 

`O(1)` for any position. 

Without SSO - we have dynamic memory allocation - when the buffer is filled, then we reallocate and mostly double, just like with vector. 
Custom allocators can change this. 

`std::wstring` wide char - certain localization tasks: 
`std::string_view` offers a non-owning view into a string, enhancing performance in specific scenarios

`std::u8string, std::u16string, std::u32string`. 


`O(n)` for linear search
`O(log n)` for sorting `std::binary_search()` is possible for sorted sequences, but usually not right. 

Special interface: 
`c_str()` returns the C-style string - that will be null terminated
`data()` direct access to underlying char data. 
`resize()` adjusts string length
`shrink_to_fit()` reduces memory usage - just like in `vector`. 

`std::vector<char>` just seems so familiar - however, it doesn't have the string semantics - eg. null termination. 

`std::string_view` stores pointer to the first char - the length of the string. 

```
void print_view(std::string_view sv)
```

// no allocation, no copying, just a new view into the string
```
std::string_view sv = "Hello, World";
std::string_view sub = sv.substr(7, 5); // world, start and length
```

```
std::string_view a = "apple";
std::string_view b = "apple";
if(a < b)
```
This is supported in `string_view` as well. 

Remember this weird thing - use `+=`with string concatenations in for loops. 

Preallocate memory wisely: 
Iterative Modulation Prudence: altering a string during iteration can serve you surprises. 

Multiple string appends - utilize `reserve()` to pre-allocate adequate memory. 

Lean on `std::string`'s member functions `find()` `replace()` and `substr()`. 

Embrace `std::string_view`. 

`std::basic_string` template, cater to custom char types and specialized char behaviours. 

UTF-16 and 32 - then `std::wstring`
ASCII and UTF-8 are find with normal `std::string`. 

Capitalize on SSO - somewhere between 15 and 23 chars. 

`std::stringstream` a great way to concatenate and convert string. 
Prefer direct string operations for real - time systems. 

STL provides us with string to i `std::stoi` and `std::to_string` among others, these are safer and often more efficient than manual parsing. 

