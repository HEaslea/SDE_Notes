Abstracting memory management from containers, so that they can all use a consistent interface for memory operations. 


We can then provide our own allocation, if we want custom memory allocation behavior. 

1. Allocate
2. Deallocate Raw Memory
3. Construct objects in memory
4. Destroy objects



#### Rough Implementation: 
```
#include <iostream> 
#include <memory>  // for standard allocator traits
#include <stddef>  // for std::size_t

template <typename T> 
class MyAllocator
{ 
public: 
	using value_type = T;

	MyAllocator() noexcept {}

	template <typename U>
	MyAllocator(const MyAllocator<U>&) noexcept {}
	
	T* allocate(std::size_t n)
	{ 
		if (n == 0) return nullptr;
		if(n > max_size()) throw std::bad_alloc();
		return static_cast<T*>(::operator new(n * sizeof(T))); 
	}

	void deallocate(T* p, std::size_t) noexcept 
	{ 
		::operator delete(p); // free memory
	}

	// construct an object of type T at the given memory location
	template <typename ... Args> 
	void construct(T* p, Args&& ... args) // universal reference
	{ 
		new(p) T(std::forward<Args>(args)...); 
	}

	// destroy object of type T at  the given memory location
	void destroy(T* p) noexcept
	{ 
		p->~T(); // explicitly call the destructor
	}

	std::size_t max_size() const noexcept
	{
		return std::numeric_limits<std::size_t>::max() / sizeof(T);
	}

	bool operator==(const MyAllocator&) const { return true;} 
	bool operator!=(const MyAllocator&) const { return false;}
};

template <typename T, tyepanem U> 
bool operator==(const MyAllocator<T>&, const MyAllocator<U>) { return true; } 
bool operator!=(const MyAllocator<T>&, const MyAllocator<U>&) { return false; }
```


## With `std::vector`
```
template <typename T, Alloc = std::allocator<T>>
class Vector
{ 
	Alloc _allocator;
	T* data; 
	size_t sz;
	size_t cap; 
	
	void helper_resize() 
	{ 
		if(sz >= cap)
		{ 
			size_t newCap = (cap == 0) ? 1 : cap*2;

			T* newData = allocat.allocat(newCap);

			for(size_ t i = 0; i < sz; ++i)
			{ 
				alloc.construct(newData + i, std::move(data[i])); // move
				alloc.destroy(data + i);  // then destroy
			}
		}
	}
}
```




```
// From the Coding Jesus Video: Interface
template <typename T, typename Allocator = std::allocator<T>>
class Vector
{ 
public: 
	Vector(size_t count, const T& item); // item, count times
	Vector(const std::initialize_list<T> items);

	// COPY CONSTRUCTOR
	// COPY ASSIGNMENT OPERATOR
	// MOVE CONSTRUCTOR
	// MOVE ASSIGNMENT OPERATOR
	// DESTRUCTOR
	
	void reserve(size_t capacity);
	void push_back(const T& item);
	
	template <typename .. Args> 
	void emplace_back(Args& ... args);

	void clear();

	size_t size() const 
	{ 
		return size_;
	}

	size_t capacity() const 
	{ 
		return capacity_;
	}

	T* data() const 
	{ 
		return data_;
	}

	Allocator get_allocator() const 
	{ 
		return { };
	}
};

template <typename T, typename Allocator> 
void Vector<T, Allocator>::reserve(size_t newCap)
{ 
	if(newCap <= capacity_)
		return;

	// 1. allocate memory
	Allocator allocator; 
	T* allocatedMemory = allocator.allocate(newCap);

	// 2. Copy over existing objects
	if(data_ != nullptr)
	{ 
		for(size_t i {}; i < size_; ++i)
			new (allocatedMemory + i) T{data_[i]};

		// 3. Destruct existing objects
		for(size_t i {}; i < size_; ++i)
			data[i].~T();

		// 4. then deallocate existing objects
		allocator.deallocate(data_, capacity_);
	}
	capacity_ = newCap;
}
```
That's a rough idea of how it works. 
