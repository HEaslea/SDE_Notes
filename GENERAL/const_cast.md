#const_cast
Here are some castings: 
```
const int i0 = 5;
// int i1 = const_cast<int>(i0); compilation error
int i2 = (int)i0;


int i3 = 5;
// const int i4 = const_cast<const int>(i3); compilation error
const int i5 = (const int)i3;
```
This is because you are not using a reference or a pointer. You don't cast `const` away/ add const. 
These are copies. 

The type that you should use is a ref or a pointer, otherwise you could just use this copying idea: 
```
int i1 = i0;
const int i4 = i3;
```

For pointers, you can cast away the const, because dereferencing the pointer will yield another type for a `const T*`