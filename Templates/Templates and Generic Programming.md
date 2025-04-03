#templates
OOP does not know type until run time, whereas, generic programming knows the types at compilation. 
Containers, iterators, algorithms are all generic programming examples. 

In generic programming, we supply the type(s) or value(s) on which that instance of the program will operate. 

The generic definition, say `vector` and then we further define into various types that the vector can hold. 

Templates are foundation of generic programming. 
They are blueprints or formulae for creating classes or functions. 
If we supply a specific type, say to a `vector`, then we are transforming that blueprint into a specific class/object or function. 
This will happen during compilation. 

### Defining a Template
Reduces boiler plate if we need several functions that do the same thing, just with various types. 

##### Function Templates
Rather than writing out lots of overloaded functions, just utilising different types: 
```
template <typename T>
int compare(const T &v1, const T &v2)
{ 
	if(v1 < v2) return -1; 
	if(v2 < v1) return 1;
	return 0;
}
```
In a template, the parameter list cannot be empty at this stage, in template specialization, it can be: 
```
template <>
int compare<int>(const int a, const int b){ 
	// something something something
}
```
This is if we want to do something different if "overloaded" function takes an int, compared to the other, broader template. You wouldn't need to do this if we are simply overloading, ie. if, in this case, only the int fucntion will be used. 
```
int compare(int,int);
```
If that's it, then it's fine. 

A function parameter list defines local variables, but does not say how to initialize them (therefore they have space in memory, just with no values appointed to them). Then when we supply arguments, there and then we are initializing them. 

Template parameters represent types or values used in the definition of a class or function. When we use a template function or class we are defining the types that are to be used, and binding them. 

At compile time, that type is determined. 

### Instantiating a Function Template
When we instantiate a function template, the copmiler will use the arguments of the call to deduce the types. 

Here the compiler will instantiate a specific function, depending on the type argued into it. 

```
// instantiates int compare(const int&, const int&)
cout << compare(1,0) << endl; // T is int

// instantiates int compare (const vector<int>, const vector<int>&)
vector<int> vector1{1,2,3}, vec2{4,5,6};
cout << compare(vec1, vec2) << endl; // T is vector<int>
```

The compiler will generate code for the type that we supply to these template functions: 
```
int compare(const int& v1, const int &v2)
{ 
	if(v1 < v2) return -1;
	if(v2 < v1) return 1;
	return 0;
}
```

Then the same for `vector<int>`, these are compiler instantiated functions. 

##### Template Type Parameters
Here, our template only has one template type parameter. 
We can utilise this "type", in the same way that we use built-in or class type specifier. 

It can be used to name the return type or a function parameter type, and for variable declarations or casts inside the function body. 
```
// ok; same type used for the return type and parameter
template<typename T> T foo (T* p)
{ 
	T tmp = *p; // temp will have the type which p points
	// ... 
	return tmp;
}
```

Each type parameter must be preceded by the keyword `class` or `typename`: 
```
// error as must Precede U with with either typename or class
template <typename T, U> T calc(const T&, const U&); 
```

The keywords `class, typename` have the same meaning and can be used interchangeably inside a template parameter list. 
```
// ok, no distinction between class and typename in this context
template <typename T, class U> calc(const T&, const U&);
```

Try and use class when passing in a class type, or just use class all the time. 
Typename was added later, therefore you might exclusively see `class` in older code, or new code written by ancient people. 

#### NonType Template Parameters
#nontype

A nontype parameter represents a value rather than a type. 
They are specified using a specific type name instead of `class` or `typename` keyword. 

We can write a version of `compare` that will handle string literals. 
Remember that such literals are arrays of `const char`. 

As we will want to compare the lengths of the two literals, we'll give our template two nontype parameters, that will be defined upon compilation time (which the length of an array is available at that time). 

```
template<unsigned N, unsigned M> 
int compare(const char(&p1)[N], const char(&p2)[M])
{ 
	return strcmp(p1, p2);
}
```
When we `compare`: 
```
compare("hi", "mom");
```
Compiler will use size of the literals to instantiate a version of the template with the sizes substituted for `N` and `M`. 
The argument bound here must a constant expression. Arguments bound to a pointer or reference nontype parameter must have static lifetime. 

We may not use an ordinary (non`static`) local object or dynamic object, as that is not instantiated at compile time. 

A pointer parameter can also be instantiated by `nullptr` or zero-valued constant expression. 

A template nontype parameter is a constant value inside the template definition. 
They can be used when constant expression are required, e.g. to specify the size of an array. 

So any nontype template parameters must be constant expressions.

##### `inline` and `constexpr` Function Templates
A function template can be declared `inline, constexpr`, in the same way as nontemplate functions. 
like this: 
```
// ok inline specifier follows the template parameter list
template <typename T> inline T min (const T&, const T&);

// error, inline in the wrong place
inline template<typename T> T min etc... 
```

#### Type-Independent Code
The `compare` that we had earlier highlight two things: 
- the function parameters in the templates are references to `const`.
- the tests in the body use only `<` comparisons. 

`const` ensuring that our function can only be used on types that cannot be copied. Eg. `unique_ptr` and `IO`. 

Also the references help that if we use a large object, then we are not copying it. 

Then, only using the `<` we are reducing the requirements on our types that we pass in. 
The types only need support `<` and not `>` too. 

We could've even done something like this: 
```
template <typename T> 
int compoare(const T& v1, const T& v2)
{ 
	if(less<T>()(v1, v2)) return -1;
	if(less<T>()(v2, v1)) return 0;
	return 0;
}
```
The problem with the original might be that if we call it on two pointers and they do not point to the same array, then our code is undefined. 

> **Templates should try to minimize the number of requirements placed on the argument types**

### Template Compilation
#templatecompilation
Compiler will not generate code until we instantiate a specific instance of it. 

When we call a function, the compiler need only see the declaration for the function. 
When we use objects of class type, the class definition must be available, but the definitions of the member functions need not be present. 
That's why we put our class definitions and function declarations in header files and definitions of ordinary and class-member functions in source files. 

Templates differ; to generate an instantiation, the compiler needs to have the code that defines a function template or class template member function. 
Therefore, headers for templates typically include definitions as well as declarations. 

> Definitions of function templates and member functions of class templates are ordinarily put into header files. 

> **Key Concept**: Templates and Headers
> Templates contain two kinds of names: 
> - Those that do not depend on a template parameter
> - Those that do depend on a template parameter
> It is up to the provide of a template to ensure that all names that do not depend on a template parameter are visible when the template is used. 
> The template provider must also ensure that the definition of the template, including definitions of the members of a class template, are visible when the template is instantiated. 

> It is up to the users of a template to ensure that declarations for all functions, types, and operator associated with the types used to instantiate the template are visible. 

> Both are these are easily satisfied by well-structured programs that make appropriate use of headers. 
> Authors of templates should provide a header containing the template definition along with the declarations for all the names used in the class template or in the definitions of its members. 
> Users of the template must include the header for the template and for any types used to instantiate that template. 


#### Compilation Errors are Mostly Reported During Instantiation
Code isn't generated until a template is instantiated, that affects when we learn about compilation errors in the code inside the template too. 
There are three stages that the compiler might flag an error. 
The first begin when we compile the template itself.. 
The compiler won't really find many issues at this point, however, it will detect syntax errors, such as forgetting a semicolon, or misspelling a variable name - not much else can really be found here. 

The second is when the compiler sees a use of the template. Here, there is still not much that we can see. If it's a function again, then we will check whether the arguments are correct, eg. the right number. We might check that we have the arguments in the right typing, if only one template used, then we would need to check that the args are both the same type. Still not much else we can really do here. 

The third is when we instantiate. Type-related errors  can be found here. They may be reported at link times. When writing a template, the code may not be overtly type specific, but template code usually makes some assumptions about the types that will be used. 
Eg. here with the `compare` fucntion
```
if(v1 < v2) return -1; // requires object to have < for type T
if(v2 < v1) return 1; // same again of both types too
return 0;
```

Assuming here that the argument type has a `<` operator. The compiler will verify that just the `<` operator for those types that are passed in are fine. 
It won't really check whether the `if` statement is valid yet (ie. converting to bool). 
```
Sales_data data1, data2;
cout << compare(data1, data2) << endl;
// error as Sales_data doesn't have < operator
```

This will instantiate a version of `compare` that will not compile. However, the compiler will not realise that it's an error until the instantiation of `compare` specifically on type `Sales_data`. 

> It is more up to the caller to guarantee that the arguments passed to the template support any operations that the template users


 The error given when I pass a type to a template function, in which there are operators that the type doesn't support: 
 In this case I passed in my own object `Derived`, which has no support for the `<` operator, the `compare` function is basically the same. 
 We get these errors here: 
 ```
 binary `<`: no operator found which takes left-hand operand of type `const T` (or there is no acceptable conversion).
```

However, if it wrote something like this in `Derived`: 
```
bool operator<(const Derived& other){ 
	return name.size() < other.name.size();
	// where name is just a data member
}
```
The caller has to be sure that the template that they are using will guarantee the functioning of the type that are passing in. 

Usually, it will and should be made more obvious.

[[Class Templates]]
