#variadic
#variadictemplate
Can take a varying number of parameters. 
The varying parameters are known as parameter pack. 
Can be 0 or more. 

We use an ellipsis to indicate that a template or a function parameter represents a pack. 

eg. template parameter list, might contain `class...` or `typename...`. 
```
// Args is a template parameter pack
// rest is a function parameter pack
// rest represents 0 or more function parameters
template <typename T, typename... Args>
void foo(const T& t, const Args&... rest);
```
`foo` is a variadic function, one type parameter named `T` and a template parameter pack named `Args`. 
That pack represents zero or more additional type parameters. 
The function parameters are that of `T` const ref. The function parameter pack named `rest`. This also represents zero or more function parameters. 

The compiler will then deduce the template parameter types from the function's arguments. 
Also for the variadic, will deduce the number of parameters in the pack. 

```
int i = 0; double d = 3.14; string s = "how now brown cow";
foo(i, s, 42, d); // three parameters in the pack
foo(s, 42, "hi");// two parameters in the pack 
foo(d, s); // one parameter in the pack
foo("hi"); // empty pack
```
There will be a few different instantiations of `foo`: 
```
void foo(const int&, const string&, const int&, const double&);
void foo(const string&, const int&, const char(&)[3]);
void foo(const double&, const string&);
void foo(const char[3]&);
```
`T` is deduced from the type of the first argument. 
The remaining arguments (if any) provide the number of, and types for, the additional arguments to the function. 

### The `sizeof...` Operator
If we need to know how many elements there are in a pack, just use `sizeof...` operator. 
Like `sizeof`, `sizeof...` returns a constant expression, does not evaluate its argument. 
```
template <typename... Args> void g(Args... args)
{ 
	// no. of type parameters
	cout << sizeof...(Args) << endl; 

	// no. of function parameters
	cout << sizeof...(args) << endl;
}
```

#### Writing a Variadic Function Template
You could just use a an `initializer_list` to define a function that takes a varying number or arguments. 
They must all have the same type, or types that are convertible to a common type. 
Variadics are used when we don't know the types nor the number of parameters. 

They are often recursive. 
The first call processing the first arg, and the subsequent calls on the remaining arguments. 
We'll write a function that does that. 
In order to stop, we have to define a nonvariadic `print` function that will take a stream and an object: 
```
// function to end the recursion, and print the last element 
// must be declare before the variadic version of print is defined
template <typename T> 
ostream &print(ostream& os, const T& t){ 
	return os << t;
}

template <typename T, typename... Args>
ostream &print(ostream &os, const T&t, const Args&... rest)
{ 
	os << t << " , ";
	return print(os, rest...); // recursive call
}
```


The first version stops the recursion, prints the last arg. 
When we call `print(cout , 42)`, we are calling the nonvariadic functions. 
Here though the variadic is also viable, the nonvariadic function is more specialized, therefore that will be picked: 
> **A declaration for the nonvariadic version of `print` must be in the scope when the variadic version is defined. Otherwise, it will just call itself**

#### Pack Expansion
The only other thing that we can do with a parameter pack is to `expand` it. 
When expanding we provide a pattern to be used on each expanded element. 
Expanding will separate the pack into its constituent elements, applying the pattern to each element as we do so. 
We trigger an expansion by putting an elipsis (...) to the right of the pattern. 

Our `print` already uses this: 
```
template <tyename T, typename... Args>
ostream& 
print(ostream &os, const T& t, const Args&... rest)
{ 
	os << t << ", ";
	return print(os, rest...). // expand rest
}
```

The first will expand the template parameter pack, and generate the function parameter list for `print`. 
The second appears in `print`. Generating the argument list for the call to `print`. 

The expansion of `Args` applies the pattern `const Args&` to each element in the pack. 
The expansion will be comma separated, a list of 0 or more parameter types, each will have the form `const Type&`. 
`print(cout, i, s, 42); // two parameters in pack here`
We just put `const&` in front of the types that we have passed in. 
```
ostream& 
print(ostream&, const int&, const string&, const int&);
```
`rest` will be expanded to a comma separated list of the elements in the pack above. 


We can expand more complicated patterns also, when we extend a function parameter pack. 
We might write a second variadic function that calls `debug_rep`, on each of its arguments and then calls `print` to print the resulting `string`s. 

```
// call debug_rep on each argument in the call to print
template <typename... Args>
ostream& errorMsg(ostream& os, const Args&... rest)
{ 
	// print(os, debug_rep(ai), debug_rep(a2), ... , debug_rep(an))
	return print(os, debug_rep(rest)...);
}
```
The call to `print` uses the pattern `debug_rep(rest)`. 
`debug_rep(rest)...` Expanding with each element in the pack `debug_rep(element)`. 


There pattern says that we want to call `debug_rep` on each element in the function parameter pack `rest`. 
The resulting expanded pack will be a comma-separated list of calls to `debug_rep`. 
A call like: 
`errorMsg(cerr, fcnName, code.num(), otherData, "other", item);`
Will then execute as if we had written: 
```
print(cerr, debug_rep(fcnName), debug_rep(code.num()), debug_rep(otherData), debug_rep("othereData"), debug_rep(item));
```

In contrast, the following pattern would fail to compiler: 
```
// passes the pack to debug_rep
// print(os, debug_rep(a1, a2, ..., an))
print(os, debug_rep(rest...)); // ERROR; no matching function to call
```
Just remember that the `...` is expanding. We don't want to do that here.

The problem here is that we expanded `rest` in the call to `debug_rep`. This call would then look like this: 
```
print(cerr, debug_rep(fcnName, code.num(), otherData, "otherdata", item));
```
Here we expanding in the call, therefore we are giving `debug_rep` five arguments. 
If the `debug_rep` was variadic, then this would be fine too. 

>**The pattern in an expansion applies separately to each element in the pack**




## Own Notes: 
```
template <typename T, typename... Args> 
void print(T first, Args... args)
{ 
	cout << first << " ";
	print(args...); // recursive call for the next arg
	// essentially going through the list of args one by one
}

// defined a simple struct named Simple with name data variable
int main(){ 
	Simple f("martin");
	print("Hugo", 1, 3.14, f); // Simple does have a <<
}
```

If were to compile this, it would run into an error, as we do not have a base case to full in the last argument that we have: Therefore we have to write another template function for the last arg in `Args`. 

```
// we have to add this before
template <typename T> 
void pritn(const T& t){ 
	cout << t << endl; // here iwll use simple << 
}
```
So we need to create a nonvariadic `print` function that will take a stream and an object. 


Summing the elements of a Parameter Pack
```
int sum(){  // base case function
	return 0;
}

template <typename T, typename... Args> 
T sum(T first, Args... rest)
{ 
	return first + sum(rest...); 
	// recursive calling, sum is being passed the expansion, which is fine, as it is variadic itself.
}

// initializer list might be better if we are just passing in ints
```
How we might check that all arguments are of the same time: 

```
template <typename T>
bool all_same(T){ 
	return true;
}

template<typename T, typename U, typename... Args> 
bool all_same(T first, U second, Args... rest)
{ 
	return std::is_same<T, U>::value && all_same(first, rest...);
}
```



#packing
#unpacking
Something the book doesn't mention that I think is very important.
They can be packed and unpacked. 
If the ellipses are to the left of `Args`, the parameter will be packed. 
If it is to the right `Args`, it will be unpacked. 
The pattern that we usually follow with variadics, operate on the first element, recursively invoke to the same variadic function. 
There has to be a finite number of steps. 
The boundary condition is that of a fully specialized template. 

```
void myPrintF(const char* format)
{ 
	cout << format;
}
 // ... Args is packing into Args

template <typename T, typename ... Args>
void myPrintF(const char* format, T value, Args ... args)
{ 
	for( ; *format != '\0'; format++){ 
		if(*format == '%')
		{ 
			std::cout << value; 
			myPrintF(format + 1, args...); // unpack
			return;
		}
		cout << *format;
	}	
}

int main(){ 
	myPrintF("\n");
	myPrintF("% world% %\n", "Hello", '!', 2011);
	myPrintF("\n");
}
```

The idea that I think is true is this: 
`functionForEach(rest)...` is unpacking everything with the pattern like this: `functionForEach(argOfRest). 


```
int sum(){ 
	return 0;
}

template <typename T, typename ... Args> 
T sum(T first, Args ... args)
{ 
	return first + sum(args...);
}

int main(){ 
	auto val = sum(3, 5, 3.14, 'A'); // val will be int
	cout << val << endl; // 10
}
```


##### Forwarding Parameter Packs

We can use variadic templates together with `forward` to write functions that pass their arguments unchanged to some other function. 
We'll write a version of `emplace_back` now. 
The `emplace_back` member is a variadic member template, that uses its arguments to construct and element directly in the space managed by the container. 
We saw that preserving type information is a two step process.
1. define `emplace_back`'s function parameters as rvalue references to a template type parameter.
2. We must use `forward` to preserve the argument's original types when `emplace_back` passes those arguments to `construct`. 

```
class StrVec{ 
public: 
	template<class ... Args> void emplace_back(Args&&...);
};
```
Another: 
```
template <class...Args>
inline 
void StrVec::emplace_black(Args&& ... args)
{ 
	chk_n_alloc(); // reallocates the StrVec if necessary
	alloc.construct(first_free++, std::forward<Args>(args)...);
}
```
Basically ignore the ideas of the members here. 

The main point to think about here is this: 
`std::foward<Args>(args)...`

This will expand both the template parameter pack, `Args`, and the function parameter pack `args`. 
This pattern will generate elements with eh form 
`std::foward<T_i>(t_i)`
Where T<sub>i</sub> and t<sub>i</sub> are ith elements in the template parameter and function parameter pack respectively. 

If we call: 
`svec.emplace_back(10, 'c'); // adds cccccccccc as a new last element` 

The pattern in the call to construct will expand to
`std::forward<int>(10), std::forward<char>(c)`. 

By using `forward` in this call, we guarantee that if `emplace_back` is called with an rvalue, then `construct` will also get an rvalue. 

`svec.emplace_back(s1 + s2);// using the move constructor`. 
The argument will be passed: 
`std::foward<string>(string("the end"))`

> **Advice: Fowarding and Variadic Functions**
> These variadic functions utilise `forward` a lot to other functions. 
```
// fun has zero or more parameters each of which is
// an rvalue reference to a template parameter type
template <typename ... Args> // packing using ... before
void fun(Args&& ... args) // again packing args, unpacking types to them as rvalue references to the types they are passed in as
{ 
	work(std::forward<Args>(args)...);
}
```
>We can really pass any type to `fun` as it is a universal reference. The type information will be passed to `work`. 


