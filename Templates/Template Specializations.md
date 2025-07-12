The `compare` function that we wrote earlier is a good example of this, it is not appropriate for many types, namely, character pointers. 
If we wanted `compare` to do something specifically when we pass a certain type, then we have to specialize. 
```
// first version, that can compare any two types
template<typename T> int compare(const T&, const T&);

// second version to handle string literals
template<size_t N, size_t M>
int compare(const char (&)[N], const char(&)[M]);
```

This version, that has two nontype template parameters. They are called `nontype` as N and M here are not really types, they are objects/values. 
Here we will be able to pass in string literals, and char pointers. 

```
const char* p1 = "hi", *p2 = "mom";
compare(p1,p2); // calls the first template
comapre("Hi", "Mom");
// These still call and instantiate the first template
```
Let's fix it;
Let's do some specializing: 
#### Defining a Function Template Specialization
We have to supply arguments for every template parameter in the original template. 
We have to use `template<>` in order to show that we are specializing here. 

```
// special version of compare to handle pointers to char arrays
template<> 
int compare(const char* const& p1, const char* const& p2)
{ 
	return strcmp(p1,p2);
}
```

The function parameter types here are insane. 
In order to define a specialization, the function parameter type(s) must match the corresponding types in a previously declared template. 
Here we are specializing for this: 
`template<typename T> int compare(const T&, const T&)`

The interaction between template parameter types, pointers, and `const` can be surprising. 

We want to define a specialization of this function with `T` as `const char*`. 
Our function requires a reference to the `const` version of this type. 
This would be the `const` version of a pointer type is a constant pointer as distinct from a pointer to `const`. 
So here we need, a reference to a `const` pointer to a `const char`. 

#### Function Overloading versus Template Specializations
When we do this, we are taking over the job of the compiler, essentially. 
A specialization is an instantiation; it is not an overloaded instance of the function name. 

> **Specializations instantiate a template; they do not overload it. As a result, specializations do not affect function matching**

If we define something as a nontemplate function (rather than a specialization), calls resolve slightly differently, as the compiler will try and use the nontemplate function more often. 

>**Key Concept : Ordinary Scope Rules Apply to Specializations**
>The declaration for the original template must be in scope. MORE importantly, we have to have the specialization in scope, before we call it, otherwise the compiler will just instantialize it itself, without using yours, which can just run into errors. 
>The compiler will not give you an error here either, it will find a function to call. Compilers just won't detect, it doesn't need to, if it instantiations what you have already specialized. 
>Templates and their specializations should be declared in the same header file. Declarations for all the templates with a given name should appear first, followed by any specializations of those templates. 

#### Class Template Specializations
As the example, we want to define a specialization of the library `hash` template that we can use to store `Sales_data` objects. 
As default, the unordered containers use `hash<key_type>`. 

The specialization should use: 
- An overloaded call operator, that returns a `size_t` and takes an object of the container's key type. 
- Two type members, `result_type` and `argument_type`, which are the return and argument types, respectively, of the call operator. 
- The default constructor and copy-assignment operator (which can be implicitly defined). 

The only issue we have when we want to specialize the class template, we have to do so in the same namespace in which the original template is defined. 
We'll say more about namespaces later on. 

For now, we can know that we have to add members to a namespace. 

To do so, we must first open the namespace: 
```
// open the std namespace, to specialize std::hash
namespace std{ 
}// close, no ; after
```

Any definitions that appear between the open and the close curlies will be part of the `std` namespace.
The following will define a specialization of `hash` for `Sales_data`. 
```
// open the std namespace we csan specialize std::hash
namespace std{ 
	template<> // defining a spec with
	struct hash<Sales_data> // the template parameter of Sales_data
	{ 
		// the type used to hash an unordered container must define these types
		typedef size_t reuslt_type;
		typedef Sales_data argument-type; 
		// by default, this type needs ==

		size_t operator()(const Sales_data& s) const;
		// our class uses synthesized copy control 
		// and defualt constructor 
	};


	size_t
	hash<Sales_data>::operator()(const Sales_data& s)
	const { 
		return hash<string>()(s.bookNo)^
			hash<unsigned>()(s.units_sold)^
			hash<double>()(s.revenue);
	}
} // closing the std namespace; note no semicolon.
```

Our `hash<Sales_data>` starting with `template<>` indicating that we ware defining a fully specialized template. 
The specialized type that we are defining here is `Sales_data`. 

Remember that a good hashing function will mostly yield a different result for objects that are not equal, no collisions for `==` objects. 

Here, we delegate defining a good hashing function to the lib. 

The `^` here is like an `xor` operator. 

Here we are using `hash<string>` object to generate a hash code for `bookNo`, an object of type `hash<unsigned>` to generate a hash from `units_sold`, and an object of type `hash<double>` to generate a hash from `revenue`. 
They are `xor`'d in order to form an overall hash code for the given `Sales_data` object. 

We have to hash all three data members so that our `hash`  function will be compatible with our definition of `operator==` for `Sales_data`. 
Assuming our specialization is in scope, it will be used automatically when we use `Sales_data`, as a key to one of these containers. 
```
// uses hash<Sales_data> and Sales_data operator==
unordered_multiset<Sales_data> SDset;
```

We have to make this specification of `hash` a friend of `Sales_data` as it uses the private data members: 

```
template <class T> class std::hash; // needed for friend declaration
// brining into scope, sort of

class Sales_data{ 
	friend class std::hash<Sales_data>;
}
```

> In order for users of `Sales_data` to use the spec of `hash`, we should define this specialization in the `Sales_data` header.


#### Class-Template Partial Specializations
We can specify some, but not all, of the template parameters or some, but not all, aspects of the parameters. 
A **class template partial specialization** is itself a template. 

We must supply arguments for those template parameters that are not fixed by the specialization. 

> We can partially specialize only a class template. We cannot partially specialize a function template.

Earlier, we introduced the library `remove_reference` type. 
That works through a series of specializations:
```
// original, most general template
template <class T> struct remove_reference{ 
	typedef T type;
};

// partial specializations that will be used for lvalue and rvalue references
template <class T> struct remove_reference<T&> // lvalue references
	{typedef T type;}

template <class T> struct remove_reference<T&&> // rvalue references
	{typedef T type;}
```
The two at the bottom are the template specializations of this original base template. 

The partial specializations, have the same name `T` as the original. 

After the class name, we specify arguments for the template parameters we are specializing. These are listed in the `<>`. The arguments correspond positionally to the parameters in the original template: 
The template parameter list of a partial specialization is a subset of, or a specialization of, the parameter list of the original template. 
In this case, the specializations have the same number of parameters as the original.. 
The parameter's types will be different. 
The specs will be used for lvalue and rvalue reference types: 

```
int i; 

// decltype(42) is int, uses the original template
remove_reference<decltype(42)>::type a;

// decltype (i) is int&, uses first (T&) partial spec
remove_reference<decltype(i)>::type b;

// decltype(std::move(i))is int&&, using second partial spec
remove_reference<decltype(std::move(i))>::type c;
```
All three `a,b,c` have the type `int`. 

#### Specializing Members but Not the Class
Rather than specializing the whole class, we can specialize the member functions. 
If `Foo` is a template class with a member `Bar`, we can specialize just that member: 
```
template <typename T> struct Foo{ 
	Foo(const T& t = T()): mem(t) {}
	void Bar(){ /*...*/ }
	T mem;
	// other members of Foo
};

template<> // specializing a template
void Foo<int>::Bar() // specializing the Bar member of Foo
{ 
	// do whatever specialized processing that applies to ints
```
```
Foo<string> fs; // instantiates Foo<string>::Foo()

fs.Bar(); // instantiates Foo<string>::Bar()

Foo<int> fi; // instantiates Foo<int>::Foo()
fi.Bar(); // using the specialization of 
// Foo<int>::Bar();
```

Using, not with `<int>`, will instantiate whatever it is that we are arguing. 

This is really basic level, just the syntax of accessing an specializing

That's the whole thing apparently. lol

###### Own Notes: 
```
template <typename T, typename U> 
class MyClass{ 
public: 
	void print() { 
		std::cout << "General Template" << endl;
	}
};

// partial specialization
template <typename T> 
class MyClass<T, int>{ 
public: 
	void print();// .... 
};

// full specialization
template <>
class MyClass<double, int> { 
public: 
	void print(); // ...
};
```