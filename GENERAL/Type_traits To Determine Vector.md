```
template<typename T> 
struct is_vector : std::falsetype {};
```

Here by default we are assuming that T is not a vector: 

Then we have the specialization: 
```
template <typename T, typename Alloc> 
struct is_vector<std::vector<T, Alloc>> : std::true_type {};
```

