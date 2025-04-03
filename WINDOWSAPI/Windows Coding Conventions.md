# Typedefs
Vast majority of APIs consist of either functions or Component Object Model (COM).
#### COMs
A COM is a platform independent, distributed, object-orientated system for creating binary software components that can interact. 

They aren't usually provided as C++ classes. 
Unlike `GDI+` #GDI, one of 2-D graphics APIs. 

The `Windows` header contains a lot of typedefs. 

##### Integer Types
![[Pasted image 20240407184615.png]]
There is a certain amount of redundancy in these. 
These are fixed size, they are the same in both 32-bit and 64-applications. 


#### Boolean Type
![[Pasted image 20240407193804.png]]Most functions that return a `BOOL` can return a non zero type, therefore, we shouldn't ever use something like this: 
```
if(result == TRUE) // never do this
```
As true might be above the typedef of TRUE that is 1. 
eg. if the result returns 2, meaning it is true, if we say `result == TRUE` then we are resulting that to false. As `2 != 1`. 
We might be able to get on with `result >= TRUE`. 
However the better way to do it is this (using normal if, that evaluates anything above 0 to true). 
```
if (SomeFunctThatReturnsBool())
{
...
}

// or

if (SomeFunctThatReturnsBool() != FALSE)
{ 
...
}
```

`BOOL` is an integer type here, not interchangeable with C++'s `bool`. 

#### Pointer Types
Many data types of the form `pointer-to-x`. They usually have a prefix of `P-`or `LP-`. 
`LPRECT` is a pointer to `RECT`, where `RECT` is a structure that describes a Rectangle. 
These are all equivalent: 
```
RECT* rect; // pointer to the rect structure
LPRECT rect; // the same
PRECT rect; // also the same
```

On 16 bit architectures, there are 2 types of pointers, P for `pointer` and LP for `long pointer` (or far pointers). Long Pointers were needed to address memory ranges outside of the current segment. 
The LP has been used to make it easier to port 16-bit code to 32-bit windows. 
There is no distinction today, and these pointer types are all equivalent. 
**Avoid using them, if you have to use one, then use P**.

The following datat types are always the size of a pointer - meaning 32 bits wide in 32-bit applications, and 64-bits wide in 64-bit applications. 
This is determined at compile time. 
When a 32-bit application runs on 64-bit, these data types are still 4 bytes wide. 
However, a 64 bit application cannot run on a 32 bit application. 

- DWORD_PTR
- INT_PTR
- LONG_PTR
- ULONG_PTR
- UINT_PTR

They are used in situations where an integer might be cast to a pointer. 
They are also used to define variables for pointer arithmetic and to define loop counter that iterate over a full range of bytes in memory buffers. 

### Hungarian Notation
The practice of adding prefixes to the names of variables, to give more information about the variables. 

`i` means an `index`, `cb` means a size in bytes ("count of bytes"), and `rw/col` mean `row` and `column` numbers. 
If we saw: `rwPosition + cbTable` we could assume easily that this is an error as we are adding a row number to a count of bytes number. 

Even for `dw` for DWORD and `w` for WORD. 

