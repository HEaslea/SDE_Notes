Removing the issue of fragmentation, at least as much as we can.
Pretty heavily used in most game engines, where testing can be left on in order to check for devastating fragmentation of memory that might lead to a crash. 
Remembering that the heap is super slow, and pre-allocation is super useful. 

We need a way to have a bunch of memory blocks, be able to get to them very easily. 
`freeList` -> 
Then we need free pool size, allocated size, perhaps considering blocks as well. 

```
template <typename T> 
class MemoryPool
{ 
public: 
	explicit MemoryPool(size_t poolSize);
	~MemoryPool();

	T* allocate();
	void deallocate(T* ptr);

private: 
	struct Node
	{ 
		Node* next;
	};

	Node* freeLists;
	size_t poolSize;
	T* pool;
};
```

[GitHub of a basic Memory Pool](https://github.com/cacay/MemoryPool/blob/master/C-11/MemoryPool.h)
Notice that there is a `.h` and the `.tcc`, using the inclusion model, because we include the `.tcc` file at the end. 

```
template <typename T, size_t BlockSize = 4096>
class MemPool
{ 
public:  // this seems so unnecessary but we'll see
	typedef T           value_type;
	typedef T*          pointer;
	typedef T&          reference;
	typedef const T*    const_pointer;
	typedef const T&    const_reference;

	template <typename U> struct rebind
	{ 
		typedef MemoryPool<U> other;
	};

	MemoryPool() noexcept;
	MemoryPool(const MemoryPool& memoryPool) noexcept;
	MemoryPool(MemoryPool&& memoryPool) noexcept;
	template<class U> MemoryPool(const MemoryPool<U>& memoryPool) noexcept;

	~MemoryPool() noexcept;

	MemoryPool& operator=(const MemoryPool& memoryPool) = delete;
	MemoryPool& operator=(MemoryPool&& memoryPool) noexcept;

	pointer address(reference x) const noexcept;
    const_pointer address(const_reference x) const noexcept;

	pointer allocate(size_type n = 1, const_pointer hint = 0);
    void deallocate(pointer p, size_type n = 1);

	size_t max_size() const noexcept;

	template <class U, class ... Args> void construct(U* p, Args&& ... args);
	template <class U> void destroy(U* p);

	template <class ... Args> pointer newElement(Args&& ... args);
	void deleteElement(pointer p);

prviate: 
	union Slot_ // interesting union usage
	{ 
		value_type element;
		Slot_* next; // linked list? 
	};	
	
	typedef char* data_pointer_;
	typedef Slot_ slot_type_;
	typedef Slot_* slot_pointer_;

	slot_pointer_ currentBlock_;
	slot_pointer_ currentSlot_;
	slot_pointer_ lastSlot_;
	slot_pointer_ freeSlots;

	size_type padPointer(data_pointer_ p, size_type align) const noexcept;
	void allocateBlock();
	
	static_assert(BlockSize >= 2 * sizeof(slot_type), "BlockSize too small.");
};
```


```
// tcc
template <typename T, size_t BlockSize> 
inline typename MemoryPool<T, BlockSize>::size_type
Memory<T, BlockSize>::padPointer(data_pointer_p, size_type align)
const noexcept
{ 
	uintptr_t result = reinterpret_cast<uintptr_t>(p);
	return ((align - result) % align);
}

// constructor
template <typename T, size_t BlockSize> 
MemoryPool<T, BlockSize>::MemoryPool()
noexcept
{ 
	currentBlock_ = nullptr;
	currentSlot_ = nullptr;
	 lastSlot_ = nullptr;
	 freeSlots_ = nullptr;
}

// Copy Constructor
template <typename T, size_t BlockSize> 
MemoryPool<T, BlockSize>::MemoryPool(const MemoryPool& memoryPool)
noexcept : 
MemoryPool()
{}


// move constructor
template <typename T, size_t BlockSize>
MemoryPool<T, BlockSize>::MemoryPool(MemoryPool&& memoryPool)
noexcept
{
  currentBlock_ = memoryPool.currentBlock_;
  memoryPool.currentBlock_ = nullptr;
  currentSlot_ = memoryPool.currentSlot_;
  lastSlot_ = memoryPool.lastSlot_;
  freeSlots_ = memoryPool.freeSlots;
}
template <typename T, size_t BlockSize> 
template <class U> 
Memorypool<T, BlockSize>::MemoryPool(const MemoryPool<U>& memoryPool)
noexcept: 
MemoryPool()
{}

// move assignment i believe
template <typename T, size_t BlockSize>
MemoryPool<T, BlockSize>&
MemoryPool<T, BlockSize>::operator=(MemoryPool&& memoryPool)
noexcept
{
  if (this != &memoryPool)
  {
    std::swap(currentBlock_, memoryPool.currentBlock_);
    currentSlot_ = memoryPool.currentSlot_;
    lastSlot_ = memoryPool.lastSlot_;
    freeSlots_ = memoryPool.freeSlots;
  }
  return *this;
}

// destructor
template <typename T, size_t BlockSize>
MemoryPool<T, BlockSize>::~MemoryPool()
noexcept
{
  slot_pointer_ curr = currentBlock_;
  while (curr != nullptr) {
    slot_pointer_ prev = curr->next;
    operator delete(reinterpret_cast<void*>(curr));
    curr = prev;
  }
}



```

KEY TO REMEMBER: 
That when we put anything into a tcc file, we should be using `inline`, however, if we were to put straight into the header file, then it really wouldn't matter at all. 
This is because when we instantiate from the header file, the compiler and linker will automatically see them as unique, and will help us out, and prevent any multiple definition errors. 
However, when we use the `tcc` file, there are already definitions, and then we would run into multiple definitions when we include into other files. 

```
// header file approach
// MyClass.h
#ifndef MYCLASS_H
#define MYCLASS_H

template <typename T>
class MyClass {
public:
    void myFunction(const T& value) {
        // Function implementation
    }
};

#endif

```

```
// tcc file approach
// MyClass.h
#ifndef MYCLASS_H
#define MYCLASS_H

template <typename T>
class MyClass {
public:
    void myFunction(const T& value);
};

#include "MyClass.tcc" // Include at the end of the header file

#endif

// MyClass.tcc
#ifndef MYCLASS_TCC
#define MYCLASS_TCC

template <typename T>
inline void MyClass<T>::myFunction(const T& value) {
    // Function implementation
}

#endif
```


#### Simple Segregated Storage
Begin by partition a memory block into fixed sized chunks. 
A pool is some object that uses Simple Segregated Storage in this fashion. 
![[Pasted image 20241107204042.png]]

You cannot be asking for chunks of varying size, they have to be the same size. 
There is this idea of linked lists: 
![[Pasted image 20241107204226.png]]
The free list holds those list nodes that are not currently being used. 
When a node is deleted from a linked list, it is placed at the head of the free list. 
Then, when we want to allocate to a node, the free list is checked to see if a list node is available. 

![[Pasted image 20241107204832.png]]


Amortized time complexity, analysing the average time per operation over a sequence of operations, it helps to determine the overall efficiency of data structures or algorithms by spreading out the cost of expensive operations across multiple cheaper ones. 



```
class MemoryPool
{ 
	size_t m_objectSize;          // size of each object
	size_t m_objectCount;         // Total objects in pool
	void* m_memory;               // Pool Memory
	std::vector<void*> m_freeList;// List of free blocks
	
public: 
	MemoryPool(size_t objectSize, size_t objectCount)
		: m_objectSize(objectSize), m_objectCount(objectCount)
	{ 
		m_memory = operator new(objectSize * objectCount);

		// initialize free list with all blocks
		for(size_t i =0; i < objecdtCount; ++i)
		{ 
			void* block = static_cast<char*>(m_memory) + i * objectSize;
			m_freeList.push_back(block);
			// putting all the pointers into the vector 
			// which is the free list
		}
	}

	// destructor to free the memory pool
	~MemoryPool() 
	{ 
		operator delete(m_memory);
	}

	// Allocate memory for an object from the pool
	void* allocate()
	{ 
		assert(!m_freeList.empty() && "Memory pool Exhausted");

		void* block = m_freeList.back();
		m_freeList.pop_back();
		return block;
	}

	void deallocate(void* block)
	{ 
		 // we don't have to get rid of that memory, we just have to write over it
		 m_freeList.push_back(block);
	}
};

struct Bullet 
{ 
	int x, y;
	int velocityX, velocityY;
	Bullet(int startX, int startY, int velX, int velY)
	: x(startX), y(startY), velocityX(velX), velocityY(velY) {}
};
```

Remember that `void*` is a pointer, however, the type that it points to is not known. Essentially just pointing an an address. 
It is an opaque handle, meaning that it refers to a created object without naming a specific type. 
Another is that we are returning a pointer to an object of unknown type. In this case, the API will be available to a whole bunch of objects, in this case we will be using `bullets`, and instantiating the pool class for that, by constructing it with the right sized object size. 
Remember however, that we cannot dereference that object here, as the pointer does not know what the object is. 
Therefore the void pointer will have to be explicitly case so it can be dereferenced. 
```
int nValue = 5;
void * pVoid = &nValue;
// cannot be dereferenced, as it is a void pointer
int* pInt = static_cast<int*>(pVoid); // cast from void* to int*
cout << *pInt << endl; // can dereference pInt
```
In `C` `malloc` does return a `void*`, and it expects you to immediately cast it to whatever type that you should be using. 

```
int main() 
{ 
	const size_t bulletCount = 1000;
	MemoryPool bullePool(sizeof(Bullet), bulletCount);

	// creating a bullet
	// this could be abstracted
	void* bulletMemory = bulletPool.allocate();
	Bullet* bullet = new (bulletMemory) Bullet(10, 20, 4, -5);

	// use Bullet...


	// deallocate the bullet when done
	bullet->~Bullet(); // Explicitly call destrutor, which sucks
	// that would need abstracting away
	bulletPool.deallocate(bulletMemory);
}
```

There is a massive reduction in fragmentation of the heap, which is super important if you don't want your program to crash almost instantly. 
There is a more predictable memory usage. 
There is a improved performance, as we are not actually deallocating and allocating new memory every single time. 
![[Pasted image 20241107215428.png]]