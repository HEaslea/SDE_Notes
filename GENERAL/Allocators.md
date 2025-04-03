Allocation and deallocation of elements stored in containers. 
Most container have a template parameter of type `allocator<Type>`, type is representative of the container element. 
`vector` will have something that looks like this: 
```
template< 
	class Type, 
	class Allocator = allocator<Type>
class vector ........ // vector uses the types defined above
```
The new standard, in most cases, the default allocation can now `construct()` using move semantics. 

![[Pasted image 20240417135430.png]]
