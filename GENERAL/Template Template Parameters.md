```
template<template <typename> class T> 
class MyClass 
{ 
};
```

`T` here is a template that takes in a single `typename` as a parameter. 

So then template template parameters, we are taking in something that is itself a template. 

A simple example: 
```
template<typename <typename> class Container> 
class MyContainer 
{ 
public: 
	Container <int> data; // using the passed template int
	
	//...
};

int main() 
{ 
	// use My Container with a vector
	// otherwise we might have to say something along
}
```

Then inside this class that we defined, we instantiated the Container to a type `int`. 

Template template parameters that take in two template parameters lol: 
`template<typename <typename, typename> class Container>`
`Container<int, std::string>`. 

```
template<typename T, template<typename> class Allocator = std::allocator>
class MyVector
{ 
	std::vector<T, Allocator<T>> data; // use the allocator template
	// ...
};

int main() 
{ 
	MyVector<int> vec; // that's all you need here, the other part of the template
	// is just to say that we have another part of the type 
	// that is a templated type too
}
```
Template template parameters are more specific to **class templates**. 
Function templates don't allow for template templates the same way. 

You might use something like this: 
```
template<typename T, template<typename> class Cont> 
void exampleFunction() 
{
	Wrapper<T, Cont> wrapped; 
	wrapped.displaySize();
}
```

HOWEVER, we cannot do something like this: 
```
template<typename T, template<typename> class Cont> 
void exampleFunction(Cont<T> container)
{ 
}
```
This is because, in terms of the language itself, function templates are designed to take in specific, yet arbitrary, so could be random, however, there is a concrete type that we should take in. 

This is partly because, imagine if the compiler all had to deduce the type that the type is of in function templates. 
Deducing concrete types ie. `std::vector<int>` is fine, say where we had to deduce the `int` part, but then, this is too far `Cont<T>` we have to deduce `Cont` and then deduce whatever type is in the `Cont`. 

Overall, template template parameters allow us to pass templates themselves as arguments to other templates. 

There must be matching: 
- Number of parameters: 
- Parameter types: 
- Template Parameter Defaults
Only class templates or alias templates can be used as template template arguments. 

```
// the Container must expect only one parameter
template <template <typename> class Container> 
class Wrapper 
{ 
public: 
	Container<int> data;
};

int main() 
{ 
	Wrapper<std::vector> w;
	w.data.push_back(10);
	std::cout << w.data[0]; 
}
```

What if we have a mismatched number of parameters: 
```
template <template <typename> class Container> 
class Wrapper { ... };

int main() 
{ 
	Wrapper<std::list> w; // Error template argument does not match
}
```

##### Using Alias Templates
```
template <typename T> 
using MyList = std::list<T> // binds allocator to std::allocator<T>

template<template <typename> class Container> 
class .....

int main() 
{ 
	Wrapper<MyList> w; // this is fine now as we are matching the required parameters
}
```
Here `MyList` is an alias template that takes on type parameter and binds the second parameter (`Allocator`) to its default . 
This means that `MyList` is effectively a template that takes only one type parameter, atching the expectation of `Wrapper`. 
### Advanced Using Variadic Templates
Here we have more flexibility, which is clear, that we can pass in the first parameter, then a parameter pack, this can be 0 args, to whatever we need: 
```
template <template <typename, typename ...> class Container> 
class Wrapper { 
public: 
	Container<int, std::allocator<int>> data;
};

int main() 
{ 
	Wrapper<std::map> w; // map has multiple parameters
	// however, they are matched using parameter packs
}
```
The line: 
`template<template<typename, typename ... > class Container, typename T> `
Takes in a template with at least one type parameter. 
`Wrapper<std::vector, int>` 
`Wrapper<std::map, int>`, `map` expects there to be two parameters, which is matched by the variadic here. 

`template <template <typename , std::size_t> class Container, typename T, std::size_t N>    class ArrayWrapper`

`Container<T, N> data`;

Here `Container` expects a template with a type parameter and a non-type parameter `std::size_t`. 
`ArrayWrapper<std::array, int, 5> arrayWrapper;`
This will obviously work fine. 

##### Parameter Packs that Handle Arbitrary Parameters
```
template <template <typename ... > class Container, typename ... Args> 
class GeneralWrapper 
{ 
public: 
	Container<Args...> data;
	...
};

// specialized for std::tuple to demonstrate usage
template<typename ... Args> 
void printTuple(const std::tuple<Args...>& t)
{ 
	std::appl([](const Args& ... args) 
		{ 
			(std::cout << args << " "), ...);
		}, t);
	)
}
int main() 
{ 
	GeneralWrappe<std::tuple, int, double, std::string> tupelWrapper; 
	tupleWrapper.data = std::make_tuple(1, 2.5, "Three");
	printTuple(tupleWrapper.data); 
}
```
`template<template<typename ... > class Container>`Here our container expects at minimum 0, or more parameters. 

Here `printTuple` is a helper function. 

```
template <template <typename, typename ...> class Container, typename T, typename ... Args> 
class FlexibleWrapper
{ 
public:
	Container<T, Args...> data;

	void add(const T& value)
	{ 
		data.push_back(value); // requiring push_back here
	}

	void print() const 
	{ 
		for(const auto& elem : data)
		{ 
			std::cout << elem << " ";
		}
		std::cout << std::endl;
	}
};

int main() 
{ 
	FlexibleWrapper<std::vector, int> vecWrapper;
	FlexibleWrapper<std::deque, int> vecWrapper;
}
```

As both `vector and deque` have default parameters, then they don't need to be named when we look at the declarations. 

#### Combining with SFINAE
```
template <template <typename, typename...> class Container, 
	typename T, typename... Args, 
		typename = std::void_t<decltype(std::declval<Container<T, Args...>>().push_back(std::declval<T>()))>>
class PushBackWrapper
{ 
public: 
	Container<T, Args...> data;
	...
};

// specialization; does nothing if Container lacks push_back
template<template <typename, typename...> class Container, typename T< typename... Args> 
class PushBackWrapper<Container, T, Args..., std::void_t<>>
{ 
public:
	Container<T, Args...> data;
};
```
Our primary template here, is enabled only if `Container<T, Args...>` has a `push_back` method. 

`std::void_t` here is a utility metafunction: 
Remember that `void` type in a context of `template<class, class = void>` **does not inherently disable the template**. 

Often used with `std::void_t` or `std::enable_if`. 

`template<class, class = void>` will always match unless a specialization provides more specific criteria. The second `class = void`, defaults to `void` unless a different value is explicitly provided. 

By using `SFINAE`, a partial specialization can conditionally change the second template parameter from`void` to something else. 
`std::enable_if` or `std::void_t`. If the substitution for the second template parameter fails because the type T doesn't meet the required criteria. 

```
template <typename T, typename = void> 
struct CheckType 
{ 
	static void print() 
	{ 
		std::cout << "Primary Template : NO specific property detected"
	}
};
// partial specialization enabled only if T has a member type 'value_type'
template<typename T> S
struct CheckType<T, std::void_t<typename T::value_type>>
{ 
	static void print() 
	{ 
		std::cout << "Partial Specialization : T has member type 'value_type'.";
	}
};

// Test types
struct WithValueType
{ 
	using value_type = int;
};

struct WithoutValueType {}; // no member type named 'value_type'

int main() 
{
	CheckType<WithValueType>::print(); // outputs partial specialization
	CheckType<witoutValueType>::print(); // ouputs primary template : no specific properties
	return 0;
}	
```
Primary Template : `template <typename T, typename = void> struct CheckType` is the primary template that will match any type T because the second parameter defaults to `void`. 

Partial Specialization: 
```
template<typename T> 
strcut CheckType<T, std::void<typename T::value_type>>
```
is only enable if T has a nested type named `value_type`. 
`std::void_t<typename T::value_type>` transforms `typename T::value_type` into `void`, if T does not have a `value_type`. 
`std::void<typename T::value_type>` transforms to `typename T::value_type` into void. 

If `T` does not have a `value_type`, the substitution fails, and this partial specialization is disabled. 

The `void` type by itself **does not disable template**. 

When we have `template <typename T, typename = void>`
When we instantiate with a single type (`MyTrait<int>`) the second template parameter defaults to `void`. 
So here we ahve a primary template providing default behaviours. 

Then we partially specialize: 
```
template <typename T> 
struct MyTrait<T, std::void_t<typename T::value_type>> 
{ 
	static void print() { ... };
}
```
Here, if T has a `::value_type`, the `std::void_t` utility transforms `typename T::value_type` into `void` it it exists: otherwise, substitution fails. 

The substitution `std::void<typename T::value_type>` fails, SFINAE, we ignore and move on. 

//// Detecting Member Functions //// 

```
template <typename T, typename = void> 
struct HasFoo : std::false_type {};

template <typename T> 
struct HasFoo<T, std::void_t<decltype(std::declval<T>().foo())>> : std::true_type {};

struct WithFoo
{ 
	void foo() {}
};

struct WithoutFoo { };

int main() 
{ 
	std::cout << "WithFoo has foo(): " << HasFoo<WithFoo>::value << "\n";
	// output True
	std::cout << "WithoutFoo has foo(): " << HasFoo<WithoutFoo>::value << "\n";
	// output False
}
```
Let's take a look here: 
With `HasFoo<WithFoo>` We check the partial specialization: 
- substitutes `decltype(std::declval<WithFoo>().foo()` which is valid (void). 
- Partial spec matches `HasFoo<WithFoo, void>`
- result `std::true_type`
with `HasFoo<WithoutFoo>`
- Substitutes `decltype(std::declval<WithoutFoo>().foo()` which fails (no `foo()`). 
- SINFAE applies : partial specialization is ignored
- `HasFoo<WithoutFoo, void>` matches here. 
- result `std::false_type`
- **`template<class, class = void>` Pattern:**
    
    - Defines a primary template with two unnamed type parameters, where the second parameter defaults to `void`.
    - Acts as a **base case** or **fallback** for type traits and SFINAE-based specializations.
- **Primary Template Matching:**
    
    - The primary template will match any type `T` because the second parameter is `void`, which is a valid default.
    - It provides a default behavior or assumption (e.g., `false` in type traits).
- **Partial Specializations with Conditions:**
    
    - Use SFINAE techniques (like `std::void_t`) to conditionally enable specializations based on properties of `T`.
    - If the condition is met, the partial specialization is selected; otherwise, the primary template remains in effect.
- **Role of `void`:**
    
    - Serves as a **marker** or **placeholder** to enable or disable template specializations.
    - When used with utilities like `std::void_t`, it helps in detecting the presence of types, member functions, or other traits within `T`.

### Practical Applications

- **Type Traits:**
    - Determine properties of types at compile time (e.g., `HasToString`, `IsPointer`).
- **Enable/Disable Functions:**
    - Control function overloads or class specializations based on type capabilities.
- **Generic Programming:**
    - Create highly generic and adaptable code by conditionally altering behavior based on type characteristics.


The things to remember is that `template<typename T, typename = void> ` will **always match unless a partial specialization matches better**. This is like the last, the base case, the final. 

Think about this during the substitution part of the compiling, not right at the beginning, where we were getting stuck with the idea that templates look the same, but put a type in as T, and visualize it. 

[[How SFINAE Disables Template Specs]]

```
template <typename T, template <typename> class Cont> 
auto MakeCont(T&& ... args)
{ 
	return Cont<T>{std::foward<T>(args)...};
}
```
```
int main() 
{ 
	auto vec = MakeCont<int, std::vector>(1, 2, 3);
	auto lst = MakeCont<double, std::list>(4.5, 6.7, 8.9);
}
```

Note here, that you can't do the fancy thing and have the template deduce `T`. 
However, we can do something like this: 
```
template <typename T, tempalte <typename> class Cont> 
auto MakeContainerImpl(T&& ... args)
{ 
	return Cont<std::decay_t<T>>{std::foward<T>(args)...};
}

template <template <typename> class Cont, typename ... Args>
auto MakeContainer(Args&& ... args)
{ 
	using T = std::common_type_t<Args...>;
	return MakeContainerImpl<T, Cont>(std::foward<Args>(args)...);
}

int main
{
	auto vec = MakeContainer<std::vector>(1, 2, 3);

	auto lst = MakeContainer<std::list>(4.5, 6.7, 8.9);
} 
```