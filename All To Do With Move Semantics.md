**The moved from object will be left in an unspecified state**. Obviously when we copy, the original has to stay in its current state, and shouldn't be changed without explicit instruction to do so. 

If an object is about to be destroyed, then we should look to move from rather than to copy from, given that the move is safe and possibly cheaper than copying. 

Going to show this off with `std::vector`. 

### `std::vector`
As per the C++ `std` container and data structure books. 
At the core : 
- A pointer to heap buffer
- Size 
- Capacity (allocated size, NOT number of elements)

When we think about what we have to do to copy this over, we have to make sure that the original is left in the normal state, when we think about moving. 

Think too about the idea of having to allocate on the heap, which is **slow**. 
Then we have to copy all the elements over, which can be really really slow as well, as that will be element-wise, and if there are containers as those elements, then it just becomes a recursive mess of slow slow slow. 


However, when we move, we are going to take the pointer of the original, so that will presumably just be `null` after that. The new vector (or the one moved to) will be pointing to the same point in the heap
Leaving the source vector in an empty and valid state ("unspecified" is generally the idea, which just means there is no rule pertaining to every single container or class etc.). 
There is no copying of elements. 
Very very fast, as that is just a few pointer assignments. 

In this sense : we summarise : we change ownership of that heap allocated buffer to the new `std::vector`, leaving the old in an empty but valid state. 
Remember that integers and fudamentals cannot be moved, therefore, they just need to be changed which is really easy. 

[Great Blog about this about vectors as well](https://akrzemi1.wordpress.com/2011/08/11/move-constructor/)


### Copy Constructor
Creating a secondary object that is equal to the original : meaning that we have two objects that are the same. 

Remember that heap allocations require OS-level bookkeeping
The general idea being use heap for large or dynamic data, and stack for small short lived and fixed size data. 

In the implementation of the vector shown above : there are three pointers, pointing to the beginning of the data buffer, then to where our data actually goes to (number of elements can just be done with pointer arithmetic), and then the end of the allocation (capacity). 
When we copy, of course we don't want to just copy the pointers, that would be a shallow copy, that's what python does by default, but to the user, at least to me, this is not all that obvious. 
This shallow copy is just using a reference by the way, there is no need to 
This idea of a deep copy is really expensive. 

This becomes a real issue, when we do this, when we really don't need the second object. 

```
vector<string> updateNames(vector<string> ans)
{ 
	while(hasNewName())
	{ 
		ans.push_back(getNewName()); 
	}
	return ans; // returning the new vector
}

auto allNames = updateNames(getSavedNames()); // no benefit at all to copying here
// as the original made in the function will never be read from again
// therefore, we might as well copy elide this
```

Here `updateNames()` will prepare the vector in one place in memory, and `allNames` needs to be locaed in a different memory location. 
We need to create a copy in the new location. 
The original, however, will never be read after all this and after the copying. There is absolutely no benefit from making this into another copy. 
### Copy Elision
Compilers are more than capable and will, however, they are not required to do so. 
Although it is allowed, sometimes it's just not possible. 
In this case, I think the compiler is not allowed to elide, as we have passed `ans` by value to the function. 

### Move Constructor
Stealing as many resources as possible. 
The original does not need them anymore, however, it must be in a valid state. 

```
template <typename T> 
class vector 
{ 
	T* _begin;
	T* _end;
	T* _capacityEnd;
	
public:
	// move constructor as we are taking in an rvalue (prvalue or xvalue)
	vector(vector&& tmp) : _begin(tmp._begin), 
		_end(tmp._end), 
		_capacityEnd(tmp._capacityEnd) noexcept
	{ 
		tmp._begin        = nullptr; // leave in empty but valid state (nullptr is valid)
		tmp._end          = nullptr; // 
		tmp._capacityEnd  = nullptr; // 
	}
}
```

###### Very Important Point About This
If we did not have the last bit, with the changing of pointers to `nullptr`, this is very important when we move from an object that is going to be destroyed very very soon. 
If we do not do this, the destructor for that class will get rid of all the info that we definitely need with the new `std::vector`. By putting those pointers to `nullptr`. 

### The All Seeing Compiler
How does the compiler know. 
Most of the time, it comes down to just seeing whether the object copied from will actually be used again, if not, then we are pretty safe to move from that. 
When we copy construct from a temporary, actually, we can just move construct. This is one of those cases where us trying to optimize is just getting in the way of the compiler and its optimization schemes. 
This obviously would not work if we were to extend the life of the temporary using a reference.  

Might occur when we return by value an automatic object, for function parameter captured by value. 

Sometimes, the compiler just won't know, as implicitly, our code requires it, and we know that, but explicitly, it's not obvious when run through the compiler. 
Therefore, sometimes, we give the compiler a hint. 
```
template <typename T> 
void swap(T& x, T& y)
{ 
	T tmp(std::move(x)); // a hint to use the move constructor
	x = std::move(y);
	y = std::move(tmp); // here we need the tmp back again
}
```

The compiler here doesn't risk spoiling the value of `x`. 
We know that it is not necessary to keep around. 

If class `T` does not have a move constructor, then the compiler will resort to using the copy constructor. 

### Passing and Returning by Value
In the old days, apparently, by value always meant a copy. 
Even from C++11 this is not the case. 

**By Value**: we are interested in the value of the original, not the original itself. 

This value can be gotten from either the copy or the move constructors. 

RVO and Copy Elision - Return Value Optimization. 
If either of these are employed, they are generally regarded as being faster than copy or move. 

## Copy Elision
Eliding any unnecessary copy or move operations. 
This is one of those times where we can get in the way of optimizations, by thinking too hard, so then we have to think even harder to get out its ways. 

```
MyClass createObject() 
{ 
	MyClass obj; 
	return obj; // normally would copy or move
}
```
The compiler might just construct this object into the caller's space, therefore, we need no copy or move.

Since C++17 : Mandatory Copy Elision - the compiler must elide copies/moves in some cases. 
Something as simple as: 
```
MyClass made() 
{ 
	return MyClass(); // guaranteed elicion in C++17
}
```

RVO - Return Value Optimization - eliminate copy when returning a local variable. 
NRVO - Named Return Value Optimization - however, now local variable has a name. 

Temporary to Target - construct directly into the target object. 