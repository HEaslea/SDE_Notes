Defining a template struct that evaluates or encodes a compile time property of a type. 
Allowing the compiler to determine type related information, enabling conditional compilation paths, type checks, and more. 
Creating the `IsDenseVector` type trait to check if `T` is a dense vector. 

```
#include <vector> 
#include <type_traits>

// primary tmpelate will default to false
template <typename T> 
struct IsDenseVector : std::false_type {};

template <typename T, typename Alloc> 
struct IsDenseVector<std::vector<T, Alloc>> : std::true_type{};
```

Always remember the primary template, it will usually be defaulting to `false`. 
We are specializing here. 

Using the type trait here: 
```
template <typename T> 
constexpr bool IsDenseVector_v = IsDenseVector<T>::value;
```
This template variable is how most of the type traits will out with `_t` or `_v`
As we have inherited from `true` or `false` `_type` then we will inherit the `::value` as well. 

Using it in many a context: Basic COUT of it here
```
cout << boolalpha; 
cout << IsDenseVector_v<std::vector<int>>; // outputting true

cout <<  IsDenseVector_v<int>; // outputting false
```