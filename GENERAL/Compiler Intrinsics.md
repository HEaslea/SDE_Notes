#### C++ Compilers

Most functions are found in libraries, however, some are built into compilers. 
They are intrinsic functions, or intrinsics. 

`__assume` and `__ReadWriteBarrier`. 
Providing information to the compiler in order to change the behaviour of the optimizer. 

Another way of using them is `#pragma intrinsic()`. 
`#pragma function()`. 

`_mm_clflush` Flushing a cache line from all levels of the cache hierarchy. 
```
void flush_cache(void* address)
{ 
	_mm_clflush(address);
}
```

`_mm_prefetch` Preloading memory into the cache. 
```
void prefetch_data(const char* data)
{ 
	_mm_prefetch(data, _MM_HINT_TO);
}
```

Bit level control
**- **`_bit_scan_forward`**: Finds the index of the least significant set bit (LSB).
    
- **`_bit_scan_reverse`**: Finds the index of the most significant set bit (MSB).
    
- **`__builtin_popcount`**: Counts the number of 1 bits in an integer.**

Which is a really sneaky way of doing [[All To Do With Bits]]. 
