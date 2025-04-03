```
#template <typename T>
class CustomAllocator
{ 
public: 
	using value_type = T;

	CustomAllocator() noexcept {}

	template<typename U> 
	CustomerAllocator(const CustomAllocator<U>&) noexcept {}


	T* allocate(std::size_t n)
	{ 
		// bringing into scope all the other new operators rather than using an in class one
		return static_cast<T*>(::operator new(n * sizeof(T)));
	}

	void deallocate(T *p, std::size_t) noexcept 
	{ 
		// using global operator
		::operator delete (p);
	}

	template <typename U, typename ... Args> 
	void construct(U *p, Args && ... args)
	{ 
		// new at address p 
		// comma operator implicit to unpacking
		new (p) U(std::forward<Args>(args)...);
	}

	template <typename U> 
	void destroy(U *p)
	{ 
		p->~U();	
	}
};

int main() 
{ 
	std::vector<int, CustomAllocator<int>> numbers; 
	// therefore using our custom allocator
}
```

`::operator new(n * sizeof(T));` will only0 allocate raw memory size, nothing to construct at all.

Really simple as we are using global new and global delete operators. 
The idea is that we make a template class with the right interface, and the right preconditions and post conditions. 

General allocators have a broad applicability. 
Frequent allocation and deallocation of small chunks of memory might suffer from fragmentation if the default allocator is used. 

Embedded systems might have limited memory or real-time systems with stringent performance requirements. 

STL containers have allocators in mind. 
Memory pools. 
They allow for centralized allocation and deallocation rather than doing that randomly throughout the code. 
Also allow for safety when it comes to raw pointers. 

General allocator is better for general and broad approach to allocation, writing our own for: 
- Performance Optimizations: Access patterns change. 
- Memory Fragmentation Mitigation: Fragmentation is inefficient, especially in long running applications. 
- Specialized hardware or memory regions: straight to GPU

#### Memory Pools: 
Pre-allocate chunk of mem - distribute into smaller blocks as needed by the app. 
Faster allocation and deallocation. 
Naturally reduce fragmentation. 
Predictable behaviour. 
We can increase overhead to reduce fragmentation - always a trade off. 
Access patterns influence cache performance. 
Allocators to ensure contiguous memory - with small enough elements. 

Think about custom allocators when we have specialized workloads - real time systems
hardware constraints. 

##### Profiling
Google `gperftools` (CPU profiler). 

Intel `VTune` Profiler

AMD `uProf`

`Tracy` Profiler - for game engines specifically