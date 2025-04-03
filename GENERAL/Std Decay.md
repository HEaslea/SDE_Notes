![[Pasted image 20240924153702.png]]
so then the decay struct here, `U` is a typedef, it's also a type, of T, with a removed reference. 
Then we also have 
`std::conditional<bool B, class T, class F>`. 
Essentially, we get Type T if true, type F if false. 
With the helper type: 
```
template<bool B, class T, class F> 
using conditional_t = typename conditional<B, T, F>::type;
```

```
template<bool B, class T, class F> 
struct conditional { using type = T; };

template<class T, class F> 
struct conditional<flase, T, F> { using type = F; };
```
Seems simple enough. 
So the conditional starts of with is_array::value, value means that true or the false. 
Might look something like this: 
```
template<class T> 
struct is_array : std::false_type {};

template<class T> 
struct is_array<T[]> : std::true_type {}; // with unknown bounds

template<clas T, std::size_t N>  // with known bounds
strut is_array<T[N]> : std::true_type {};
```

We can see that if we follow that, the next part of conditional, if the first bool is true. 
`std::pointer` 