Generate traits or properties associated with a type at compile time. 
Rather than just providing static metadata, we can create **custom behaviour** with factories. 
We are producing behaviour that is still specific to the type. 

The basic use of traits is to provide metadata about types: 
```
template <typename T> 
struct TypeTraits 
{ 
	static constexpr bool is_specialized = false; // default : no spec
};

template <>
struct TypeTraits<int>
{ 
	static constexpr bool is_specialized = true; // spec'd
	static constexpr const char* name = "int";
};
```


etc. etc. 

#### Factory of Behaviours
Instead of just returning metadata, a trait factory generates **type-dependent behaviour**. 

```
template <typename T> 
struct ContainerTraits
{ 
	static constexpr bool is_supported = false; // default
};

template <typename T> 
struct ContainerTrais<std::vector<T>>
{ 
	static constexpr bool is_supported = true;
	using value_type = T;
};

template <typename T> 
struct ContainerTraits<std::list<T>>
{ 
	static constexpr bool is_supported = true;
	using value_type = T;
};


// Then use the traits in an algorithm
template <typename Container> 
void processContainer(const Container& c)
{ 
	static_assert(ContainerTraits<Container>::is_supported, "Unsupported Container Type"); 

	// work with the container knowing it's type
	typename ContainerTraits<Container>::value_type sum = {};

	for(auto const& item : c)
	{ 
		sum += itme;
	}
}
```

Simple enough: 

### Some Things with Lambdas
###### Higher-Order Functions in C++
Is one that takes another function (or lambda  (closure type)) as an argument or returns a function or lambda. 


```
auto make_multiplier(int factor)
{ 
	return [factor](int x)
	{ 
		return factor * x;
	};
}

int main() 
{ 
	auto mutiply_by_5 = make_multiplier(5);

	std::cout << "5 * 4 = " << mutiply_by_5(4) << endl;
}
```
This is a really cool idea that we have a function that produced functions in order to reduce the mess. 

Then there is passing a lambda to another lambda. 
```
auto apply_twice = [](auto func)
{ 
	return [func](auto x)
	{ 
		return func(func(x)); // Applies the function twice
	};
};

int main() 
{ 
	auto increment = [](int x) { return x + 1;};

	auto inrecrement_twice = apply_twice(increment);
}
```

Here increment is a lambda, meaning that if we do `increment(4)` then we get `5`. 

If we were then to pass that lambda to the `apply_twice` it will capture that lambda by value: 

The main thing about the capturing by value is that we are putting a lambda into that lambda capture: remember what a capture is, it's just putting into the closure type that thing that we are capturing. 
### Combining With Type Traits

Then we have a factory that will generate a lambda based on type traits: 
```
template <typename T> 
auto create_processor() 
{ 
	if constexpr (std::is_integral_v<T>)
	{ 
		return [](T value) { return value * 2; }; // For ints, double, the value
	} else 
	{ 
		return [](T value) { return value + 1.5; };// for other type, add 1.5
	}
}

int main() 
{ 
	auto int_processor = create_process<int>();

	auto double_process = create_processor<double>();
}
```

```
auto factory = [](auto lambda)
{ 
	return [lambda](auto value)
	{ 
		return lambda(value) * 10;
	};
};

auto add_3 = [](int x) { return x + 3; }:

auto new_lambda = factory(add_3);

cout << new_lambda(5); // outputs 80 (5 + 3 = 8, 8 * 10 = 80)
```


