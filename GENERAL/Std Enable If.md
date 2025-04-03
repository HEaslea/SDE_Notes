Where can I put it: 
1. Anywhere the fuck you like

Remember that the whole thing with SFINAE is that at any point in the class or function, then the whole thing will collapse, the substitution will fail, and we won't be able to use that template. 

1. Return Type
2. Parameter List
3. Template Parameter List
4. Member Variables or Member Functions -> to denote whether we can use the member function.


```
template <typename T> 
class Container
{ 
public: 
	template<typename U = T> // will be T if no arg given 
	std::enable_if_t<std::is_integral_v<U>, void> print()
	{ 
		cout << "Integral Type!" << endl;
	}

	template<typename U = T> 
	std::enable_if_t<std::is_floating_point_v<U>, void> print()
	{ 
		cout << "Floating Point Type" << endl;
	}
};
```

Return Type:
```
template<typename T> 
std::enable_if_t<std::is_integral_v<T>, T> multiply(T value)
{ 
	return value * 2;
}
```

In Parameter List
```
template< typename T, std::enable_if_t<std::is_floating_point_v<T>>>
void print(T value)
{ 
}
```

Another way: 
```
template <typename T, typename Enable = std::enable_if_t<std::is_pointer_v<T>>>
class PointerChecker
{ 
public: 
	void check()
	{ 
		std::cout << "T is a pointer type" << std::endl;
	}
}
```

```
struct A
{ 
	void operator () () 
	{ 
		cout << "A Operator () Called" << endl;
	}
}
```

