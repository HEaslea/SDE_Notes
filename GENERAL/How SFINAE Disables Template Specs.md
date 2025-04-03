Subsitution Failure is when the compiler tries to substitute template parameters with actual types or expressions, and the substitution results in an invalid type or expression. 

When this happens, it's fine, because SFINAE makes sure things don't break. 

1. Define a Primary Template: This is the valid fall-back always. 
2. Define Partial Specializations: These templates usually include conditions (using `std::void_t`, `std::enable_if`) that must be satisfied for the specialization to be considered. 
3. Attempt substitution: If conditions in the partial are met, then we choose that one. Otherwise, we move to the valid fall-back. 

### Eg Detecting a Nested Type
```
// primary template
// giving us a valid fall-back
template <typename T, typename = void>
struct HasValueType : std::false_type {};

// partial specializations
template <typename T>
struct HasValueType<T, std::void_t<typename T::value_type>> : std::true_type {};

struct WithValueType 
{ 
	using value_type = int;
};
```

