## Pointer Types
A **pointer** is a variable whose value refers to some other object. 
`M: array [0..1023] of integer`
Here is an idea that I haven't really thought about before, rather than referring to these by name, we just refer to them by index. Although this is like super obvious, that idea of just mapping, rather than arbitrarily indexing, therefore the name becomes more of a arithmetic process, like hash mapping, rather than symbolically naming them. 

Then there's this idea, where we keep the index of an object, inside the another index (as another element): 
```
M[1] := 0;
M[ M [1] ] := 100;
```

### Pointer Implementation
There are a few mechanisms that could be used. 
Some use offsets from a certain position. 
Some use **double indirection**, pointer object contains the address of some memory variable whose value is the address of the object to be accessed. 
```
// Double Indirection
mov( PointerVariable, ebx );
mov( [ebx], eax );
```

This is obviously more inefficient than just going straight there or just using a pointer, due to the fact that we might have cache misses. 

Briefly, the cache is managed by the **CPU's Cache Controller** : 
**MMU** : Translates virtual addresses (used by programs) to physical addresses, used by RAM. Handles memory protecting, paging, segmentation. Works with page tables and the TLB (Transition Lookaside Buffer). 

**Cache Controller** : Automatically checks if a requested mem is already in the cache (L1, L2 etc.), hit or miss. 

The idea being that when our code requests memory at a virtual address : the MMU translates that to a physical address : the CPU's cache controller checks if that physical address is in the cache: 

**TLB** : Transition Lookaside Buffer : small, fast cache inside the MMU that speeds up the virtual-to-physical address translation - this translation done via a page table. 
**Walking that page table is slow**. 
TLB caches recent virtual-to-physical address translations to avoid repeated lookup. 
TLB Hit : TLB Miss and we have to walk the page table. 
Typically around 16 to 512 entries. 
Fully associative and searching all entries at once.
The page table - full map of all virtual to physical mappings. 

### Pointers and Dynamic Memory Allocation
Typically pointers are for allocations on the heap. 

`malloc()/free()` in C. 
`new()/delete()` in C++. 
These are **anonymous variables** - as we get to them with addresses rather than variable names. 

There are languages, like python, that will use garbage collection, rather than having to explicitly `delete()` or `free()` that data/address. 

The pointer itself may have a name, however, the object itself does not have. 

## Point Operations and Pointer Arithmetic
We can compare, and we can add or subtract integers to the pointer address (as we assume that they are just integers at the end of the day). 

**Addresses are essentially integers** : we have a layer of abstraction over these integers that do more than just numbering. 

`ptrVar = malloc( bytes_to_allocate );`

We generally supply an expression like `sizeof(int)`. 
The `sizeof()` function returns the number of bytes needed by its single parameter. 

We might have `ptrVar = malloc( sizeof( int ) * 8 );`
Allowing for 8 integers
![[Pasted image 20250627120952.png]]

Each integer is only 4 bytes, which we get from `sizeof(int)`. 

The pointer returned is the first address. Therefore, it's very useful to know how many bytes a type is. 

In order to get to the other integers in this manner, we need to offset from that base address that is the pointer that is returned. 

So each integer is going to be 4 bytes away from the beginning of the alst. 
![[Pasted image 20250627122014.png]]

#### Adding an Integer to a Pointer
![[Pasted image 20250627122028.png]]
Notice that `EAX` is going to maintain the base address, therefore the offset is going to have to increase, rather than + 4 every time. 

In `mov( destination, memory_address )` , this means loading from the memory address as the second argument, and put it in the destination, first argument. 

Some machines won't allow us to access arbitrary addresses, only addresses that are on the boundary line. 
Therefore, our HLL has to account for that. 

Any attempt to access memory on some other boundary will raise an exception and potentially halt the application. 

Then it must supply a generic pointer arithmetic scheme that's portable across different CPU architectures. 

If we have a pointer `p` that is pointing to a 16-byte object, then p + 1 points 16 bytes beyond the address where `p` points. 
`p + 2` will be 32 bytes beyond `p`. 

**It makes no sense at all to add two pointers together** : that would be insane :). 

Taking note of the commutative property. 

##### Subtracting an Integer from a Pointer
This is not commutative. 

`p - i`  is going to be legal, however `i - p` might not be legal 

#### Subtracting a Pointer from a Pointer
In contrast with addition, it makes sense to subtract one pointer away from another, that is, to get the difference between the two. 
We get the difference in bytes, then we can use `sizeof(<type>)` in order to see how many of those objects are in-between the two pointers.  

```
int distance; 
char* aPtr;
char* ePtr;


aPtr = sometString; // get ptr to start of string in aPtr. 

// While we are not at the end of the string and the current char isn't 'a':

while(*aPtr != '\0' && *aPtr != 'a') // notice the typo in the book
{ 
	aPtr = aPtr + 1; // advance one
}

// we want the ePtr to start at the a char
ePtr = aPtr;

while(*ePtr != '\0' && *ePtr != 'e')
{ 
	ePtr = ePtr + 1; // mo 
}

// now compute the number of chars between the 'a' and the 'e'
// counting the a but not counting the e, think about that as a the difference

distance = (ePtr - aPtr); // larger minus the smaller, in all cases unless you want a negative number. 
```

Here we get the number of bytes between the two, in this case, it works out as `char`'s are only one byte anyways, so that different is going to be the number of `char`'s, however, if this was integers, therefore the number of integers between the two. 

This really only makes sense if the two pointers are in the same data structure - ie somewhat close in memory (of course a very large data structure, this might not be the case). And we want to know the bytes between, therefore, most cases, we are looking at the same type in that structure, otherwise we don't have the idea of "the number of `x` between". Of if you want to know how many bytes between parts of a class. 
The difference really is the meaning in the value obtained. 

**C++**: Will require the use of two pointers that point to the same `<type>`, as it will give you the number of objects between, not the number of bytes, make sure that you differentiate there, and not think about bytes in-between. 

C++ pointers have a layer of abstraction, where the pointer is aware of the number of bytes between two objects, therefore you can say `p + 1` in order to get to the next object. 

The smart thing to do would be to take the absolute value of the difference, so that if we have two pointers, we not sure which one is smaller or lower. 

### Comparing Pointers
Just the idea that if you have a `p1 < p2` then we can say that the `p1` object is earlier in the data structure. 

## Arrays
Aggregate, where all members are of the same type. 
We get a member by specifying the index with an integer (and only an integer). 

![[Pasted image 20250627160654.png]]
`A[i]` is the ith element in the array. 

`data_type array_name [ number_of_elements];`


```
char CharArray[ 128 ];  // 128 bytes i beleive
int intArray[ 8 ];  // 32 bytes, 4 * 8
unsigned char ByteArray[ 10 ];  // array of 10 bytes, as unsigned chars
int *PtrArray[ 4 ]; // an array of 4 pointers to ints
```

