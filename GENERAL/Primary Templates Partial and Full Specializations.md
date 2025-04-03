### Primary Templates
Defined without any specific type or value parameters. Acting as the general case for all potential instantiations. 

We then move on to create specialized versions of a primary template, to handle specific cases, for custom behaviour on those specific cases. 

```
// primary template
template <typename T> 
class MyContainer 
{ 
	// do all the generic things
};
```

### Full Specializations
We give the type that we want to define for: 
```
template <>
class MyContainer<int> 
{ 
	// specific for int
};
```

### Partial Specializations
Giving a specialization for a subset of the template parameters, not just one type. We still have some generality. 
```
// PRIMARY TEMPLATE
template <typename T, typename U> 
class MyPair 
{ 
	... do a thing generally
};

// PARTIAL SPECIALIZATION for when U and T are the same
template <typename T> 
class MyPair<T, T> 
{ 
	... do a thing if they are the same
}
```

![[Pasted image 20240928174834.png]]