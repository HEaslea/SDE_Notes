Metaprogramming is the writing of compute programs: 

1. That write or manipulate other programs (or themselves) as their data, or 
2. That do work at compile time that would otherwise be done at run tim . 

Using template instantiation to drive compile-time evaluation: 

When using name of template, the compiler will instantiate the expected entity from that template. 


Therefore, when we exploit this we get source code flexibility and run time performance. 
There is a noticeable difference in run time performance when we use this: 

```
template<int N> 
struct abs
{ 
	static_assert(N != INT_MIN); // C++17-style guard
	static constexpr auto value = (N < 0)? -N : N; // return
};
```

Here is a compile-time absolute value function: 
```
constexpr auto abs(int N) { return (N < 0) ? -N : N;}
```
This will only be done at compile time, if N is known at compile time. 
`constexpr int x = -5` will do fine!

There's a level of recursion here that is really cool, using structs and not just some template function, very cool indeed. 

```
template <class T> 
struct myrank { static constexpr size_t value = 0u;};

template<class U, size_t N>
struct myrank<U[N]>
{    
    static constexpr size_t value = 1u + rank<U>::value;
};

int main(){ 
    using array_t = int[10][20][30];
    auto f = rank<array_t>::value;// this is giving 3
```

Here, we are lookinga t that if you pass in a type that is not an array, then you get back 0: 
however, when we pass in an array, given by the specialization at below the first. 
First we are taking in an array, and recursively diving deeper into it, until we can get the rank at the bottom. 
So first, we rank of `int[10]`, then we go to `int[20]`: 
`U[10]; // where U is int[20][30]`
Then we move through them, giving us 1 + 1 + 1 + 0 = 3; 

Which is really nice. 

### Producing Types as Results
eg. removing a type's `const` qualification: 
No real removal, just moire that we are giving the equiv type that is without const: 
Now that I look at it, it looks fairly simple: 
```
template <class T> 
struct remove_const{ using type = T;};

template <class U> 
struct remove_const<const U> { using type = U;};
```


Then there is this simplicity that we have here: 
```
template <class T> 
struct type_is { using type = T;};
```
Then we can use public inheritance: 
```
template<class T> 
struct remove_volatile : type_is<T> {};
```

Then there is a version for recognizing volatile qualified types: 
```
template<class U> 
struct remove_volatile<volatile U> : type_is<U> {};
```
This is very cool indeed, as now we don't even have to rewrite the whole `using type = T;`
As we just inherit from that type. 


### Compile_Time Decision Making
Imagine a metafunction, IF/ IF_t, to select one of two types: 

## SFINAE
During template instantiation, the compiler will: 
1. Obtain (/ figure out) the template arguments: 
- Taken verbatim if explicitly supplied at template's point of us
- or deduced from function arguments, if any, at point of call
- or taken from the declaration's default template arguments. 
2. Replace each template parameter, throughout the template, by its corresponding template argument. 
Fairly obvious, we deduce, we just need to get the actual type in there for instantiation. 
If we then have well-formed code, the instantiation succeeds, however, if the resulting code is ill-formed, it is considered not viable (due to substitution failure) and is **silently discarded**.

### In Use
eg. want one algo, f, taking in types T, and overload it with a second f taking floating point types T. 
For a given type T, want at most one or two algos to be instantiated, giving: 
```
template<class T> 
enable_if_t<is_integral<T>::value, maxint_t> 
	f (T val) { ... };

template<class T> 
enable_if_t<is_floating_point<T>::value, long double> 
	f(T val) { ... };
```




##### A Quick Note on `enable_if`
Say we want to `to_String` something, like int 102 to "102". 
If we are to be given a `Person` object, then we want to return the string representation of the object, as defined by the author of the class, duh. 
If we are given vectors, say `int [1, 2, 3, 4]`, then we want to return a vector of `string ["1", "2", "3", "4"]`. 

```
template<typename T> 
void Log(const T& obj)
{ 
	/* perhaps some pre-processing here*/
	std::cout << ToString(obj); // std::cout for moment
	/* post-processing*/
}
```

So for `ToString`, there is the option to function overload, boring, not useful, not extensible, you wanna write all that?: 
It works, but it's bs. 

```
template<typename X> 
std::string ToString(const T& x)
{ 
	return std::to_string(x);
}
```

Then we can specialize as we go along: 
```
template<>
std::string ToString(const Person& x)
{ 
	return x.toString();
}
```

Where `toString()` will be part of the object's interface. 

Then we can specialize for vectors as well, however, now we are just writing overloads for non-primitive types: 
```
template <typename T> 
std::string ToString(const std::vector<T>& x)
{ 
	if(x.empty[])
	{ 
		return "[]";
	}

	std::string result = "[";

	for(const auto& ele : x) // for const T& ele surely
	{ 
		result += ToString(x) + ','; // recursive 
	}
	result.back() = ']';
}
```

If you don't mind, now write all the code for maps, lists, multimaps etc. 


## Template Metaprogramming: 
Remember that compile time constants can be used as template parameters. 
Let's further define a struct `enable_if`, taking a boolean non-type parameter and T a type parameter: 
```
template<bool B, typename T> 
struct enable_if{};

// specialization
template<typename T> 
struct enable_if<true, T> { typedef T type; };
```

This is where SFINAE comes in, "Substitution Failure Is Not An Error", in short, if you write some template code that cannot be substituted correctly, the compiler will flag it but not throw an error and not stop the compilation. Essentially, we write back code, that we don't call, and the compiler is more than happy. 
Substitution is when the the compiler is looking for and trying to figure out how to match a template to a specific type, it ignores that type if it can't do it, and just silently moves on to the next viable option. 

The substitution attempt : when trying to instantiate a template with certain types, when/if we encounter an error here, all we get is a substitution failure, not a shut down error. 
Quick example: 
```
template<typename T> 
typename std::enable?if<std::is_integral<T>::value, void>::type
printType(T val)
{ 
	std::cout << "Integer Type:: " << val << std::endl;
}

// overload for types that are not integers (floating points etc.)
template<typename T> 
typename std::enable_if<std::is_floating_point<T>::value, void>::type 
printType(T val)
{ 
	std::cout << "Floating POint Type :: " << val << std::endl;
}

int main() 
{ 
	printType(42); // integer type
	pritnType(3.14); // floating point type
}
```
The compiler tries to match the `printType` with the type fo the argument passed. 
For `printType(42)` we get `std::is_integral<int>::value` is `true`. 


There is another thing here about unnamed function parameters? 
```
void foo(int) 
{ 
	std::cout << "YO this in an INT FOOL" << std::endl;
}
```

So how would we define `is_type_container`. 
```
template<typename T> 
struct is_type_container 
{ 
	static const bool value = false; // compile time constant
};

template<typename T> 
sturct is_type_container<std::vector<T>>
{ 
	static const bool value = true;
};

template<typename T> 
sturct is_type_container<std::set<T>>
{ 
	static const bool value = true;
};
```

This just seems really obvious, however, not if this step just happens without thinking about it, we create structs that have values that are overloaded per type. 

```
template<typename T> 
std::string ToString(const T& x, 
	typename enable_if<is_type_container<T>::value, T>::type* = 0)
{ 
	/*collection implementation from before*/
}
```

If the type T is either a vector or set, the `value` is true, which makes `type` a valid entity in the program since `enable_if<true, T>` defines a typedef for it hence the function exists. 
If the value is false, then we give the function over to SFINAE. 

We can do the same with our own UDT: 
```
template <typename T> 
struct has_to_string 
{ 
	static const bool value = false;
};

template <> 
struct has_to_string<House>
{ 
	static const bool value = true;
};

template <> 
struct has_to_string<Person>
{ 
	static const bool value = true;
};

template <> 
struct has_to_string<Animal>
{ 
	static const bool value = true;
};

template<typename T> 
std::string ToString( const T& x, 
	typename enable_if<has_to_string<T>::value, T>::type* = 0)
{ 
	/* User Define implementation from before */
	return x.toString();
}
```