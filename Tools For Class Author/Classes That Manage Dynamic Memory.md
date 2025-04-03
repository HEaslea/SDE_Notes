Most classes use a lib container to hold their data. 
Remember our `StrBlob` class uses a `vector` to manage the underlying storage for its elements. 

This doesn't work for every class, some need to do their own allocation. 
When we do this, we need to manage this and the allocation. 

We'll implement a simplification of the lib `vector` class. 
This will not be a template class, it will just hold `string`s.
Therefore our class will be called `StrVec`

#### `StrVec` Class Design
Remember that `vectors` will store its elements in contiguous storage. 
`vector` preallocates enough storage to hold more elements than are needed. 
Each `vector` member that adds elements checks whether there is space available for another element. 
If so, the member constructs an object in the next available spot. 
If no space, then we reallocate; The `vector` will then obtain new space, moves the existing elements into that space, frees the old space, and adds the new element.

We'll use an `allocator` to obtain raw mem. 
This memory is not constructed, we'll use the `allocator's construct` member to create objects in that space when we need to add an element. 

Each `StrVec` will have three pointers into the space it uses for its elements: 
- `elements`, which points to the first element in the allocated memory
- `first_free`, which points just after last actual element
- `cap`, which points just past the end of the allocated memory. 


![[Pasted image 20240414154012.png]]

`StrVec` will have a `static` data member named `alloc` that will be the `allocator<string>`. This will be used almost in every function in order to acquire memory. 

Then we will have utility functions: 
- `alloc_n_copy` will allocate space and copy a given range of elements. 
- `free` will destroy the constructed elements and deallocate space. 
- `chk_n_alloc` will ensure that there is room to add at least one more element to the `StrVec`. If there isn't, we will call `realloc` in order to get more space. 
- `reallocate` will reallocate the `StrVec` when it runs out of space. 

Let's have a look at the definition: 

##### `StrVec` Class Definition

```
// simplified implementation of the memory allocation strategy for a vector-like class

class StrVec{ 
public: 
	StrVec(): // the allocator member is default initialized
		elements(nullptr), first_free(nullptr) cap(nullptr) {}

	StrVec(const StrVec&); // copy constructor
	StrVec &operator=(const StrVec&); // copy assignment
	~StrVec(); // destructor
	void push_back(const std::string&); // copy the element

	size_t size() const { return first_free - elements; }
	size_t capacity() const { return cap-elements; }
	std::string *begin() const { return elements; }
	std::string *end() const { return first_free; }

private: 
	static std::allocator<std::string> alloc; // for allocating elements

	void check_n_alloc() // used by functions that add elements to a StrVec
	{ if(size() == capacity()) reallocate();}

	// utilities used by the copy constructor, assignment operator, and destructor
	std::pair<std::string*, std::string*> alloc_n_copy
		(const std::string*, const std::string*); 

	void free(); // destroy the elements and free the space

	void reallocate(); // get more space and copy the existing elements

	std::string *elements; // pointer to the first element in the array

	std::string *first_free; // pointer to the first free element in the array 

	std::string *cap; // pointer to one past the end of the array
};
// alloc must be defined in the StrVec implementation file
allocator<string> StrVec::alloc;
```

Several members were defined here: 
- The default constructor (implicitly) default initializes `alloc` and (explicitly) initializes the pointers to `nullptr`, indicating that there are no elements. 
-  The `size` member returns the number of elements actually in use, which is equal to `first_free - elements`
- The `capacity` member returns the number of elements that the `StrVec` can hold, which is equal to `cap - elements`
- The `chk_n_alloc` causes the `StrVec` to be reallocated when there is no room to add another element, which happens when `cap == first_free`. 
- The `begin` and `end` members return pointers to the first (ie `elements`) and one past the last constructed element (ie. `first_free`) respectively. 

#### Using `construct`
The `push_back` function calls `chk_n_alloc` to ensure that there is room. 
When it returns, we use the container `push_back`. We ask `alloc` to `construct` a new last element: 

```
void StrVec::push_back(const string& s)
{ 
	chk_n_alloc(); // ensure that there is room for another element
	// construct a copy of s in the element to which first_free points
	alloc.construct(first_free++, s);
}
```

Remember that with constructors, that our memory is unconstructed. 
To use with raw memory, we must call `construct`. 
The first arg must be a pointer, to unconstructed space allocated by the call to `allocate`. 
The remaining args are used to determine which constructor to use to construct the object that will go in that space. 
The arg here is `string`, so this is a call to use the copy constructor of string. 
This here also increments the `first_free` to indicate the new element has been constructed. 
The postfix notation gives that away. 

###### The `alloc_n_copy` Member
Called whenever we copy or assign a `StrVec`. 
We have to have value-like behaviour, when we copy or assign a `StrVec`, we have to allocate independent memory and copy the elements from the original to the new `StrVec`, not just point to those elements. 

The `alloc_n_copy` will allocate enough room to hold its given range of elements, and will  copy those elements into the newly allocated space, requiring copies. 
This will return a pair or pointers, pointing to the beginning of the new space and just past the last element it copied: 
```
pair<string*, string*> 
StrVec::alloc_n_copy(const string* b, const string* b)
{ 
	// allocate space to hold as many elements as are in the range
	auto data = alloc.allocate(e-b);

	// return a pair constructed form data
	// and the value returned by uninitialized_copy
	return {data, uninitialized_copy(b, e, data)};
}
```

A little tricky to get your head around, so; we allocate using `alloc` using the range that we get from `e-b`. 
The copying happens in the return statement, which list initializes a pair. 
The `first` member of the returned `pair` points to the start of the allocated memory, that is returned by `alloc.allocate(//something);`. 
Then the `second` is the value that we are given by `uninitialized_copy` which will return the pointer positioned one element past the last constructed element. 

#### The `free` Member
The `free` idea is to `destroy` the elements and then deallocate that space that `StrVec` allocated. 
The `for` loop calls the `allocator` member `destroy` in reverse order, starting with  the last constructed element and finishing with the first. 

```
void StrVec::fre()
{  
	// may not pass deallocate a 0 pointer; if elements is 0; there's not work to be done
	if(elements){ 
		// destroy the old elements in reverse order
		for(auto p = first_free; p != elements; /* empty */)
		{ 
			alloc.destroy(--p);
		}
		alloc.deallocate(elements, cap-elements);
	}
}
```
Really here, in terms of creating this whole `StrVec` it's about range control and using pointers correctly. 
Once the elements are destroyed, we free the space that his `StrVec` allocated by calling `deallocate`. 
The pointer that we pass here, must be one that we have previously generated by a call to allocate. 
Therefore, we first check that `elements` is not null before calling it. 

#### Copy-Control Members
Given our `alloc_n_copy` and `free` members, the copy-control members of our class are straightforward. 
The copy constructor calls `alloc_n_copy`: 
```
StrVec::StrVec(const StrVec &s)
{ 
	// call alloc_n_copy to allocate exactly as many elements as in s
	auto newdata = alloc_n_copy(s.begin(), s.end());
	elements = newdata.first;
	first_free = cap = newdata.second; 
}
```
and assigns the result from that call to the data members. 
The return value from `alloc_n_copy` is a `pair` of pointers. 
The `first` points to the first constructed element and the `second` points just pas the last constructed element. 
`alloc_n_copy` allocates space for exactly as many elements as it is given, `cap` also points just pas the last constructed element, as well as `first_free`. 
The destructor calls `free:` 
`StrVec::~StrVec{ free(); }`

Remember that the copy assignment operator has to protect against self-assignment. 
That's the one that returns a reference to an object. 
Like so: 
```
StrVec& StrVec::operator=(const StrVec& rhs)
{ 
	// call alloc_n_copy to allocate exaclty as many elements as in rhs
	auto data = alloc_n_copy(rhs.begin(), rhs.end());
	free();
	elements = data.first;
	first_free = cap = data.second;
	return *this;
}
```
Like the copy constructor, the copy-assignment operator will use the values returned from `alloc_n_copy` to initialize its pointers. 

### Moving, NOT COPYING, Elements during Reallocation
#movingnotcopying
Before writing the `reallocate` member, we should think about how it should work: 
- Allocate memory for a new, larger array of `string`s. 
- Construct the first part of that space to hold the existing elements
- Destroy the elements in the existing memory and deallocate that memory

Reallocating a `StrVec` entails copying each `string` from the old `StrVec` memory to the new. 
Knowing that `string`s have valuelike behaviour. 
When we copy a `string`, the new `string` and the original `string` are independent from each other. 
Changes made to the original, are not going to affect the copy, and vice versa. 

Each `string` must have its own copy of the characters that make up that `string`. 
Copying a `string` must allocate memory for those characters, and destroying a `string` must free the memory used by that `string`. 

Here, when we copy, we destroy the original, therefore, copying really is unnecessary, why not just move the original. 

Our `StrVec`'s performance will be much better if we can avoid that overhead, allocating, deallocating. 
Therefore we should use something like `std::move`. 

Ref to `alloc_n_copy`
```
pair<string*, string*> // return type
StrVec::alloc_n_copy(const string* b, const string* e)
{ 
	// allocate space to hold as many elements as are in the range
	auto data = alloc.allocate(e - b);
	// initialize and return a pair constructed from data and
	// the value returned by uninitialized_copy
	return {data, uninitialized_copy(b,e,data)};
}
```

#### Move Constructors and `std::move`
We can avoid this copying action, using two facilities introduced in a new library. 
Seven library classes, including `string`, define so called "move constructors". 

We "move" the resources from the given object, to the object being constructed. 
We also know that the library guarantees that the "moved-from" `string` remains in a valid, destructible state, this is important, as our own objects will be left in that state too. 
For `string`, we can imagine that each `string` has a pointer to an array of `char`. 

Presumably, the `string` move constructor just copies the pointer rather than allocating new space for and copying the characters themselves. 

The second is that we might use the library function `move`. 
Defined in the `<utility>` header. 
Two points here: 
- When `reallocate` constructs the `string`s in the new memory it must call `move` to signal that it wants to use the `string` move constructor. If it omits the call to `move` then the `string` copy constructor will be used (will be explained further down the line). 
- We usually0 do not provide a `using` declaration for `move`. When we use `move`, we call `std::move` not `move`. 

### The `reallocate` Member
Using this information, we can now write our `reallocate` member. 

We'll start by calling `allocate`, creating the new unconstructed space. 
We'll double the capacity, of the `StrVec` each time we reallocate. 
If the `StrVec` is empty, then allocate for 1. 

```
void StrVec::reallocate()
{ 
	// we'll allocate space for twice as many elements as the current size
	auto newcapacity = size() ? 2 * size() : 1; // smart
	// allocate new memory
	auto newdata = alloc.allocate(newcapacity);
	// move the data from the old memory to the new
	// copying here is unnecessary
	auto dest = newdata; // points to the next free position in the new array

	auto elem = elements; // points to the next element in the old array

	for(size_t i = 0; i != size(); ++i)
		alloc.construct(dest++, std::move(*elem++));
	free(); // free the old space once we've moved the elements
	// update our data structore to point to the new elements

	elements = newdata; 
	first_free = dest;
	cap = elementss + newcapacity;
}
```
The `for` loop iterates through the existing elements and `constructs` a corresponding element in the new space. 

We use `dest` to point to the memory in which to construct the new `string`, and `elem` to point to an element in original array. 

Postfix increment to move the `dest` and `elem` pointers one element at a time through these two arrays.

The second arg in the call `construct` (the one that determines which constructor to use) is the value returned by `move`. 
`move` will return a result that cases `construct` to use the `string` move constructor. 
Using the `move` constructor, the memory managed by those `string` s will not be copied. 
Each `string` we construct will take over ownership of the memory from the `string` to which `elem` points. 
Then we call `free()` to destroy the old elements. 
Responsibility for their data has been moved to the elements in the new `StrVec` memory.

Then we just update the pointers to address the newly allocated and initialized array. 
The `first_free` and `cap` pointers are set to denote one past the last, and one past the end of the allocated space, respectively. 



#### Quick Note on Pointer Arithmetic
When we do the difference between two pointers, we are partially open inclusive. 
Simple example: 
`10 - 9 = 1` Imagine that they are both pointers to an array, this just means that 9 to 10 is one element large, even though you might think that there are two elements here, this is just a thing about working with differences. 
It's more that we are saying that 10 is 1 more than 9, which seems super obvious, but when it comes to arrays. 

So here: 
```
int arr[] = {0,1,2,3,4,5};
auto s = arr; 
auto f = arr[5];

cout << f - s << endl; // outputs 5
```


Shit I've just realised as well, that when we do copy assignment, we do need to destruct a lot on the left object, it might point to things that we need to get rid of before we give it new pointers to work with. 

[[Moving Objects]]