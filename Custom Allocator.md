### The Allocator Interface
A default `std::allocator`, has to provide member functions that align closely with its responsibilities. 
- `allocate()` - Allocate a block of me suitable for holding specified number of objects
- `deallocate()` - Returns a block of mem, previously allocated
- `construct()` - Constructs an object in a given memory location 
- `destroy()` - Calls the destructor on an object at a given memory location

## The Code
```
template <typename T> 
class MyAllocator
{ 
	using value_type = T; 

	CustomAllocator() noexcept {}

	// copy from allocator of another type
	template<typename U> 
	CustomAllocator(const CustomAllocator<U>&) noexcept {}

	T* allocate(std::size_t n)
	{ 
		// ... some logging here
		return static_cast<T*>(::operator new(n * sizeof(T)));
	}

	void deallocate(T* p, std::size_t) noexcept
	{ 
		::operator delete(p);
	}

	template<typename U, typename... Args> 
	void construct(U *p, Args &&... args)
	{ 
		new (p) U(std::forward<Args>(args)...); // the comma operator is implied
	}

	template<typename U> 
	void destroy (U* p)
	{ 
		p->~U();
	}
	
};
```