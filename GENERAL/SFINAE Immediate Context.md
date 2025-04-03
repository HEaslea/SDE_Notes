The mental model that we should be using is that the substitution needs to do a "preparation" step first to generate types and members, which might cause some hard errors. After all the necessary generation is done, any further invalid uses are not errors. 

Remember the idea of : 
### Two-Phase Name Lookup 
1. First Phase: 
- Occurring when the template is defined, not when it is instantiated. 
- The compiler will look up the name that do not depend on template parameters. 
- Errors are reported for names that the compiler can resolve at this phase. 
2. Second Phase: 
- Occurring when the template is instantiated with specific template arguments. 
- The compiler looks up names that depend on template parameters. 
- Errors are reported if the compiler cannot resolve these names with the given arguments. 

The immediate context usually involves the first phase of this lookup process. 

#### Dependent vs Non-Dependent Names
Non-Dependent, no depending on any template parameters. Resolved (easily) during the first phase. 

Dependent names, therefore, depend on the template parameters. These will have to be resolved during the second phase. 

```
tempalte <typename T> 
void printSize() 
{ 
	std::cout << sizeof(T) << std::endl; 
}
```
Here `cout` and `sizeof` are non-dependent names. 

```
template <typename T> 
void addElement(std::vector<T>& vec, const T& elem)
{ 
	vec.push_back(elem); // push_back is a dependent name
}
```
 The compiler will not be able to resolve `push_back` in the first phase, because it doesn't know that `T` is yet. 
 The compiler will defer that to the second phase, when we look at instantiation. 
Another would be: 
```
template <typename T> 
void printTypeName() 
{ 
	typename T::value_type var; // 'value_type' is a dependent tyep
	// hence the typename
}
```

The standard doesn't really give a definition of the immediate context, however, we have to think about the two phases, and dependent, and non-dependent. 

Remember too: that only the non-template function allows nontrivial conversions. 

Templated functions will not allow for nontrivial conversions, for template argument deductions. 
If there is to be a cast, then it needs to be explicit. 

```
template <typename T> 
void print_type(T t)
{ 
	std::cout << typeid(t).name() << std::endl;
}
int main() 
{ 
	print_type(42); // T deduced as Int
	print_type(3.14); // T deduced as Double
}
```

```
template <typename T>
typename std::enable_if<std::is_integral<T>::value>::type
print_type(T t)
{ 
	std::cout << "INTEGRAL: " << t << std::endl;
}

template <typename T> 
printType(T t)
{ 
	std::cout << "FLOATING POINT: " << t << std::endl;
}
```

The immediate context here is the type T deduced from the argument. `SFINAE` ensures the function is only instantiated for specific types. 


```
template <typename T> 
struct Wrapper
{ 
	using type = T;
};

template <typename T> 
void process() 
{ 
	typename Wrapper<T>::type value; // dependent on T
	std::cout << typeid(valeu).name() << std::endl;
}
```

In here `Wrapper<T>::type`  is dependent on `T`, and the immediate context (the template parameter `T`) determines how it is resolved. 

```
template <typename T, typename Enable = void> 
struct MyClass
{ 
	void print() { std::cout << "GENERAL TYPE: " << std::endl; }
};

template <typename T> 
struct MyClass<T, typename std::enable_if<std::is_integral<T>::value>::type>
{ 
	void print() { std::cout << "INTEGRAL TYPE : " << std::endl; }
};

template <typename T>  // remember the parameters here
struct MyClass<T, typename std::enable_if<std::is_floating_point<T>::value>::type> // speciailize here
{ 
	void print() { std::cout << "FLOATING POINT: " << std::endl; }
};
```

The thing with the parameters here is that the top part `template <typename T>` is for the input, that is what we have to deduce during the look up phase, and instantiation, however, the second part, the specialization that we put on the template class, that is how we are specializing. 
The top part is what we are bringing in, then the specialization is to say, we will use this if we get these specializations. 
"Given the input parameter `T`, choose this instantiation if `T` meets certain criteria". 

**DON'T FORGET DEDUCTION GUIDES**. 

```
template <typename T> 
struct MyClass
{ 
	T value;
	MyClass(T v) : value(v) {}
};

template <typename T> 
MyClass(T) -> MyClass<T>; // deduction guide
```

When the compiler sees this, we know it will allow for `MyClass(T) ->MyClass<T>` that when we pass an argument of type T to the constructor, the template parameter `T` should be deduced. 


```
template <typename T, typename U> 
struct MyClass
{ 
	T t_val; 
	U u_val; 
	MyClass(T t, U u) : t_val(t), u_val(u) {}
};

template <typename T, typename U> 
MyClass(T t, U u)  -> MyClass<T, U>;
```

Here is one that makes way more sense, where our construction called actually deviates from that which we might expect: 

```
template <typename T, typename U> 
struct MyClass
{ 
	T value;
	U type;

	MyClass(T val) : value(val), type(&value)  {} // U is inferred as a pointer to T
	void print() 
	{ 
		std::cout << "Value: " << value << " , TYPE: "  << type << std::endl;
	}
};

template <typename T>
MyClass(T) -> MyClass<T, T*>;
```

This means that we don't have to pass a type for U. 

Doing something like this: 
`template <typename T, T* U>` would mean that we have do something along the lines of: 
`MyClass<int, int*>`. 
The deduction guide means that we don't have to do that. 




