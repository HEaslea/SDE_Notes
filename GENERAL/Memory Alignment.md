##### `std::align()`
Given a pointer to a buffer of size space, will return a pointer by the specified alignment for size (the size of the buffer) number of bytes and decreases space argument by the number of bytes used for this alignment. 
If not possible, will return null ptr. 
**This behaviour will be undefined for any `alignment` that is not a power of 2 (ie. has one bit)**

```
template <std::size_t N> 
struct MyAllocator
{ 
	char data[N]:
	void *p;

	std::size_t sz;

	// ctor
	MyAllocator() : p(data), sz(N) {}

	template <typename T> 
	T* aligned_alloc(std:size_t a = alignof(T))
	{ 
		if(std::align(a, sizeof(T), p, sz)) // if not nullptr
		{ 
			T* result = reinterpret_cast<T*>(p);
			p = (char*)p + sizeof(T); // p moves on one byte, see below
			sz -= sizeof(T);
			return result;
		}
		return nullptr;
	}
};
```

If we have a `int* p`, then `p + 1` would move it 4 bytes over: 
`(char*)p + 1` will move it one byte over, this is really crucial. 
Pointer arithmetic will use the size of the type that we are using, 1 in char bytes is just 1 byte. 
1 in `int` byte will have to be 4 bytes in order to make that work. 

`alignof()` - Returning the alignment of bytes, required for any instance of the type indicated by the `type-id`. 

The idea is that: say we have a `4 byte int` that will require an address that is a multiple of 4, if the int were to be stored at `0x1003`, then the CPU might need a couple of time, as the address is not divisible by 4. 
Put it at `0x1004` then there is no additional overhead. 
Even putting the int at `0x1002`, not being divisible by 4. 

Here `0x1002` to `0x1003` typically represents 1 byte (8 bits) in modern systems. 
Memory and words: A word in our architecture is 64 bits, can be 32 bits in others. 
64 bits is 8 bytes. 
This is clearer on 32 bit systems, where we are getting less memory every time, and so we have to make sure that our alignment is correct: 
```
Address:    0x1000       0x1008       0x1010
Data Fetched: [8 bytes]   [8 bytes]    [8 bytes]
               ^--- int is within this block
```

Remember our hexadecimal here. 
If the int were put at `0x1002` then it will straddle two 8 byte memory blocks. 
```
Address:    0x1000       0x1008       0x1010
Data Fetched: [8 bytes]   [8 bytes]    [8 bytes]
              ^--- first half of int
                   ^--- second half of int
```

This looks fine, that an int is 4 bytes, then we start at `0x1002` then end at `0x1006`, however, **THE CPU expects the `int` to be aligned at an address** of a multiple of 4. 

CPU's will perform better when the data that they are accessing is aligned to its natural bounds. 
Even though we are fetching data in 8 bytes at a time, the **CPU will still perform better if the data is naturally at its bounds**. 

### `alignas`
```
struct MyStruct
{ 
	char a;  // 1 byte 
	int b; // 4 bytes, misaligned by default
};

int main() 
{ 
	alignas(16) MyStruct s; // Align s to a 16 byte boundary
	cotu << "Alignment of s: " << alignof(MyStruct) << endl;
	cout << "Size of s: " << sizeof(s) << std::endl;
	
}
```
`alignas(16) struct MyStruct` - Aligns the entire structure
`alignas(64) int x;` - Aligns the variable `x` to a 64 byte boundary. 

```
struct alignas(16) AlignedStruct 
{ 
	char a; // 1 byte
	int b; // 4 bytes
	double c; // 8 bytes
	// 
};

```



My Little Alg that gets the power of 2 that is higher than the `sizeof(T)`
```
template <typename T>
long checkHighest(const T& s)
{ 
	using type = T; 
	long currSize = 1;
	
	while(currSize < sizeof(type))
		currSize <<= 1;
	return currSize;
}
```
Really simple. 




```
template
```