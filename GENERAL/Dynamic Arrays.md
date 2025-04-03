If we allocate for vector or arrays, then we have to allocate several areas at the same time. 

We have a new `new` expression, offering us a new way of doing this.

The library provides a template class named `allocator` that lets us separate allocation from initialization. 
An `allocator` in general is very useful and makes things even easier than they are already. 

Many apps have no need for dynamic memory. 
Mostly we just want to use a vector. 

Most applications should use a library container rather than dynamically allocated arrays. 
Using a container is easier, less likely to contain memory-management bugs, *and* is likely to give better performance. 

Classes that use dynamic arrays have to define their own versions of these operations to manage the associated memory when objects are copied, assigned and destroyed. 

These ideas are made more clear in the next chapter. 

#### `new` and Arrays
`new` will allocate the requested number of objects and return the pointer to the first one: 
```
// call get_size to determine how many ints to allocated
int *pia = new int[get_size()]; // pia points to the first of these ints
```

The int inside the [] doesn't have to be a `const`. 
We might also do something like
```
typedef int arrT[42]; // typedef names the type of array of 42 ints as arrT
int *p = new arrT;

// will execute as if we have written
int *p = new int[42];
```

##### Allocating an Array Yields a Pointer to the Element Type
Instead of really creating the object with an array type (which is not what we do when we create an array in dynamic memory). Instead we get a pointer to the element type of that array. That is true of a typedef as well. 

We cannot call a `begin, end` function on the array. These functions use the array dimension (which is part of an array's type) to return pointers to the first and one past the last elements, respectively. 
For the same reasons, we are not going to be able to use a range `for` to process the elements  in  a (so-called) dynamic array. 

To be clear, the allocated memory does not have an array type, leading to all these issues here. 

```
int *pia = new int[10]; // block of ten uninitialized ints
int *pia2 = new int[10](); // block of ten ints value initialized to 0
string *psa = new string[10]; // block of ten empty strings
string *psa2 = new string[10](); // block of ten empty strings 
```

We might also do something like this: 
```
// block of ten ints initialized from the corresponding initializer
int *pia3 = new int[10]{0,1,2,3,4,5,6,7,8,9};
// block of ten strings; the first four are initialized from the given initializers
// remaining elements are value initialized
string *psa3 = new string[10]{"a", "an", "the", string(3, 'x')};
```


#### It Is Legal to Dynamically Allocate and Empty Array
We can use an arbitrary expression to determine the number of objects to allocate: 
```
size_t n = get_size(); // get_size returns the number of elements needed
int* p = new int[n]; // allocate an array to hold the elements
for(int* q = p; q != p + n; ++q){ 
	// process the array or element
}
```

If `get_size()` here returns 0, we're still fine as our for loop doesn't meet the condition and doesn't loop. 

```
char arr[0]; // error: cannot define a zero-length array
char *cp = new char[0]; // fine but cp can't be dereferenced
```

The pointer that we are returned here is "one past last", for a zero element array. 
It cannot be used to dereference, as it points to no elementss. 

##### Freeing Dynamic Arrays
#freeingdyanmicarrays
We now need a special delete; 
```
delete p; // p must point to a dynamically allocated object or be null 

delete[] pa; // pa must point to a dynamically allocated array or be null
```
They will be destroyed in reverse order, last element first right. 

```
typedef int arrT[42]; // arrT names the type array of 42 ints
int *p = new arrT;
delete[] p; // brackets are needed as it is an array
```
`p` points to the first element of an array of objects, not to a single object of type `arrT`. Therefore we have to use [] here. 


##### Smart Pointers and Dynamic Arrays
There is a version of `unique_ptr` to manage a dynamic array, we must include a pair of empty brackets after the object type: 
```
// up points to an array of ten unitiailized ints
unique_ptr<int[]> up(new int[10]);
up.reset(); // automatically uses delete[] to destroy its pointer
```

`unique_ptr` will automatically use `delete[]`. 

When a `unique_ptr` points to an array, we cannot use a dot and arrow member access operators, as, it points to an array, not an object. 
We can use the subscript operator: 
```
for(size_t i = 0; i != 10; ++i)
	up[i] = i; // assign a new value to each of the elements
```

![[Pasted image 20240409180458.png]]
`shared_ptr`s do not do this. If we want to use one to control a dynamic array, then we need to supply our own deleter. 

```
shared_ptr<int> sp(new int[10], [](int* p){ delete []p;});
sp.reset();
```
If we didn't give a deleter, this could is undefined. 

We also have to change how we access elements: as they don't have subscript operators like `unique_ptr`s. 
```
// shared_ptrs don't have subscript operators and don't support pointer arithmetic
for(size_t i = 0; i != 10; ++i){ 
	*(sp.get() + i) = i; // use get to get a built-in pointer
}
```

This has to be reviewed, there's a lot of smart pointer stuff that's still confusing. There's a BtB video about it somewhere I'm sure. 

An aspect of `new` that limits its flexibility is that `new` combines allocating memory with constructing object(s) in that memory. 
`delete` does the same in reverse, destruction with deallocation. 

When we allocate a block of memory, we often plan to construct objects in that memory as needed. 
In this case, we'd like to decouple memory allocation from object construction (that word again, coupling - the idea of joining together to abstract modules in programming). 
Decoupling construction from allocation means that we can allocate memory in large chunks an pay the overhead of constructing the objects only when we actually need to create them. 

Coupling allocation and construction can be wasteful: 
```
string *const p = new string[n]; // construct n empty strings
string s; 
string *q = p; // q points to the first string
while(cin >> s && q != p + n)
	*q++ = s; // asign a new value to *q, postfix for assigning first
const size_t size = q - p; // remember how many strings we read
// use the array
delete[] p; // must remember to use [] as p points to an array, however in dynamic memory, it is not an array type
```

The `new` expression allocates and initializes `n strings`. 
We may have created objects here that are never used. 
Even for the ones that we do use, we immediately assign new values over the previously initialized `string`s. 

More importantly, classes that do not have default constructors cannot be dynamically allocated as an array. 


#### The `allocator` Class
`allocator` class, in the `memory` header, lets us separate allocation from construction. 

Providing type-aware allocation of raw, unconstructed, memory. 

![[Pasted image 20240410115804.png]]

`allocator` is a template class, pass in whatever type that you want to. 
All it does it allocate the memory space, not constructing any objects in there. 

```
allocator<string> alloc; // object that can allocate strings
auto const p = alloc.allocate(n); // allocate for n unconstructed strings
```

##### `allocator`s Allocate Unconstructed Memory
This is unconstructed memory here. 
We use this memory by constructing objects in that memory after the fact.
We use `construct` in order to take the pointers and 0 or more arguments. 

As usual the arguments have to be matching a constructor for that object type. 

```
auto q = p; // q will point to one past the last constructed element
alloc.construct(q++); // *q is the empty string
alloc.construct(q++, 10, 'c'); // *q is cccccccccc
alloc.construct(q++, "Hi"); // *q is hi
```

Early on, `construct` only took two arguments; the pointer at which to construct an object and a value of the element type. 

Unconstructed memory is undefined. 

We can call `destroy` on each constructed element. 
The `destroy` function takes a pointer and runs the destructor on the pointed - to object. 
```
while(q! = p)
	alloc.destroy(--q); // free the strings from end to front. 
```

`q` is decremented before we call `destroy`. 
You can only `destroy` elements that are constructed. 

We can then, after destruction, set the memory free for re-allocation: 
`alloc.deallocate(p, n);`
Pointer here obviously can't be null, and the `n` the size for freeing memory, must be the same as the `allocation` `n`. 
So if we pass in a `size (n)` parameter, then we might want to make that `const`. The fact we are passing it in at all, means that we are cementing how much memory we are going to be allocating. 

#### Algorithms to Copy and Fill uninitialized Memory
As a companion to the `allocator` class, the library also defines two algorithms that can construct objects in uninitialized memory. 
They are defined here: 
![[Pasted image 20240410153844.png]]
Assume we have a vector of `ints` that we want to copy into dynamic memory. 

We'll allocate memory for twice as many `int`s as are in the `vector`. 
We'll construct the first half by copying elements from the original `vector`. 
Then the second we'll do with a given value: 
```
// alocate twice as many elements as vi holds
auto p = alloc.allocate(vi.size() * 2);

// construct elements starting at p as copies of elements in vi
auto q = uninitialized_copy(vi.begin(), vi.end(), p);
// q points to one past the last element of construction

// initialize the remaining elements to 42
uninitialized_fill_n(q, vi.size(), 42);
```

So here we have to be careful that the memory that we have allocated for this, is large enough to fill everything we need in the copy/ fill. 

Unlike `copy`, `uninitialize_copy` constructs elements in its destination. 

#### [[BookExample]]






Coding Example from CPPREF: 
```
allocator<int> alloc;

  

    // static_assert(std::is_same_v<int, decltype(alloc1)::value_type>);

    // used to check that alloc is for allocating ints

    int* p1 = alloc.allocate(1);

    alloc.deallocate(p1, 1); // and it's gone

  

    using traits_t1 = std::allocator_traits<decltype(alloc)>;

    p1 = traits_t1::allocate(alloc, 1);

    traits_t1::construct(alloc, p1, 7); // constructing the int 7

    cout << *p1 << '\n';

    traits_t1::deallocate(alloc, p1, 1); // deallocate space for one int

  

    std::allocator<string> alloc2;

  

    using traits_t2 = allocator_traits<decltype(alloc2)>;

    // rebinding allocator using trait for strings gets the same type

    traits_t2::rebind_alloc<string> alloc_ = alloc2;

  

    string* p2 = traits_t2::allocate(alloc2, 2); // space for 2 strings

    traits_t2::construct(alloc2, p2, "foo"); // p2 was at the beginning of the

    // allocated space

  

    traits_t2::construct(alloc2, p2 + 1, "bar");

  

    cout << p2[0] << "  :  " << p2[1] << '\n';

  

    // can use subscripting here if we have allocated n spaces

  

    traits_t2::destroy(alloc2, p2 + 1);

    traits_t2::destroy(alloc2, p2);

    traits_t2::deallocate(alloc2, p2, 2);
```


[[Traits]]


[[NoThrow]]
```
#include <new>
int main(){ 
	int* ptr = new (std::nothrow) int[1000000000000];
	// no throw will not throw error, however will just return a null ptr on failure to create new array of ints 
	if(ptr == nullptr){ 
		cout << "Mem allocation failed" << endl;
	} else { 
		cout << "Mem allocation successful" << endl;
		delete[] ptr;
	}
}
```
