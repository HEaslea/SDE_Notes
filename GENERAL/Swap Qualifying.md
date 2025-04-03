Remember ages ago when we were first learning all the C++ stuff. 
When we came across something akin to `std::swap` and using `std::swap`. 

The idea was and is, that swap is supplied as a template function: 
```
template <typename T> 
void swap(T& a, T& b)
{ 
	T tmp(std::move(a));
	a = std::move(b);
	b = std::move(tmp);
}
```
That is the general idea, however, what if we are to provide our own version, within our own type here. 

```
namespace custom 
{ 
class CustomType
{ 
	/* Implementation that requires a special form of swap */
};

// free function helper swap function for Type CustomType
void swap(CustomType& a, CustomType& b)
{ 
	/* Special Implememntation for swapping two instances of type 
	CustomType
	*/
}
```

Then we might have something that looks like this: 
```
template <typename T> 
void some_function(T& value)
{ 
	T tmp(/* ... */); 
	using std::swap; // this line is crucial
	swap(tmp, value);
}
```

The idea here that if we have to qualify `swap` with `std::swap` then we cannot use the ADL version of swap, that will be found when we put in `tmp, value`. Therefore we need to bring that swap into scope. This means that if `T` is a type that has its own version of swap, then we wouldn't be able to call it at all if we were to qualify `std::swap`. 
Bringing it into scope, we are able to call the `std::swap` or the type version of `swap` without qualifying, meaning that we are opening ourselves up to more possibilities. 
ADL allows us to find the Type dependent version of this, and since there is no qualifying going on, we add it to the possible set. 
This is super important. 

The using inside the function, brings it all into scope, lacking the qualifying, we are able to use many more functions, using the same call `swap()`. 
