Mirrored in "Modern C++". 

#templateargumentdeduction
By default, the compiler uses the arguments in a call to determine the template parameters for a function template. 
The process is known as **Template Argument Deduction**. 


### Conversions and Template Type Parameters
Arguments are used to initialize the (mostly) local parameters that the function has. 

Function parameters with template type parameter have special initialization rules. 
Only a very limited number of conversions are automatically applied to such arguments. 
Rather than converting, the compiler generates a new instantiation. 

Top-level `const` in either parameter or arguments are ignored: The only other conversions performed in a call to a function template are: 
- `const` conversions; a function parameter that is a reference (or pointer) to a `const` can be passed a reference (or pointer) to a non-`const` object. Passing in a reference to a non`const` to a function that takes a reference to a `const` is fine. 
- Array - or function-to-pointer conversion: if the function parameter is not a reference type, then the normal pointer conversion will be applied to arguments of array or function type. An array - decay too pointer to the first element. A function arg will be converted to a pointer to the function's type too. 

Other conversions, eg. arithmetic conversions, derived-to-base, user-defined conversions are not performed. 

Consider these calls: 
```
template <typename T> T fobj(T, T); // arguments are copied into
template<typename T> T fref(const T&, const T&); // references

string s1("a value");
const string s2("another value");

fobj(s1, s2); // calls fobj(string, string); const is ignored
fref(s1, s2); // calls fref(const string&, const string&)
// uses premissible conversion to const on s1

int a[10], b[42];
fobj(a,b); // calls f(int*, int*)
fref(a, b); // error as array types don't match
```

In the first calls, we pass a `string` and a `const string`. 
Though the types do not match exactly, both calls are legal. 
The call to `fobj`, the arguments will be copied, so whether the original object is `const` doesn't matter. 
In the all to `fref`, the parameter type is a reference to `const`. Conversion therefore to `const` for a reference parameter is a permitted conversion, so this call is legal too. 

In the next calls, passing arrays, in which are different size. In the call to `fobj`, this difference in size doesn't matter. 
Both arrays are converted to pointers to int* ,`T` is consistent. 
The template parameter type for in `fobj` is `int*1`. 

The call to `fref` however, is illegal, you will go to jail for that one. 

When the parameter is a reference, the arrays are not converted to pointers. 
The types of `a` and `b` here, don't match, so the call is in error, as `T` is not consistent. 

> `const` conversions and array or function to pointer are the only automatic conversions for arguments to parameters with template types. 

```
template <typename T> 
void f(T param);
```
From the "modern C++", we ignore `const` and `reference` type.
This is because we are creating a copy. This is true just for by value parameters. 
If we have parameters that are references to, or pointers to , then we preserve the `const`ness. 
Eg. 
```
const char* const ptr = "Fun with Pointers";
f(ptr); 
// Type deduced, const char*, a modifiable pointer to const char
```

Top-level `const-ness` is ignored, therefore the deduced type here is `const char*`. 

Then: 
```
template <typename T> 
void f(T&& param);
```

Basically everything passed in here keeps its type, just goes to lvalue; 
`f(const int)` param's type is `const int&`. 
If we pass in an rvalue `f(27)` ; param's type is therefore deduced to be `int&&`. 

Just read the "modern C++" here in order to understand here: 

#### Function Parameters that Use the same Template Parameter Type
A template type parameter can be use as the type of more than one function parameter. 
There are limited conversions, the args to such parameter must have essentially the same type. 
If the deduced types, do not match, then the call is in error. 
eg. `compare` taking two `const T&` parameters. 
```
long lng; 
compare(lng, 1024);// Error, cannot instantiate compare(long, int), as the types don't match
```

If we want to allow conversions on the arguments, we will define the function with two type parameters:
```
// arg types here can differ, but must be compatible
template<typename A, typename B> 
int flexibleCompare(const A& v1, const B& v2)
{ 
	if(v1 < v2) return -1;
	if(v2 < v1) return 1;
	return 0;
}
```
The user may supply differing types (they don't have to), the operator `<` must be able to compare the values of those types. 

##### Normal Conversions Apply for Ordinary Arguments
Converting to the corresponding type of the parameter: 
```
tempalte <typename T> ostream &print (ostream &os, const T& obj)
{ 
	return os << obj;
}

print(cout , 42); // instantiates print(ostream&, int)
ofstream f("Output");
print(f, 10); // uses print(ostream&, int); converts f to ostream&
```
The second call, as the type of the ostream parameter does not depend on the type of a template parameter, the compiler will implicitly convert `f` to `ostream&`. 
Therfore 
> **normal conversions are applied to arguments that are not a type deduced in template parameter**

#### Function-Template Explicit Arguments
Sometimes, it is not possible for the compiler to deduce the types of the template arguments. In others, we want to allow the user to control the template instantiation. 

Both cases arise when a function return type differs from any of those used in the parameter list. 

###### Specifying an Explicit Template Argument
Wanting to let the user specify which type to use: We'd like the user to specify the type of the result. 
Here the user can determine what level of precision is appropriate: 
```
// T1 cannot be deduced; it doesn't appear in the function parameter list 
template <tyename T1, typename T2, typename T3> 
T1  sum(T2, T3);
```

The caller here has to provide an **explicit template argument**. 
```
// T1 is explicitly supplied: T2 and T3 are inferred from the arg types that we pass in in ()
auto val3 = sum<long long>(i,long); // long long sum(int, long)
```
They are matched, from left to right; 
A poorer design would be to put the return type at the end of the template parameter list, as that would mean we have to supply the other two, due to trailing deduction: 
```
// poor design
template <typename T1, typename T2, typename T3> 
T3 alternative_sum(T2, T1); 
```

This just really makes no sense in general, such poor design habits in this: 

```
// can't infer the return type from this
auto val3 = alternative_sum<long long> (i, lng);

// ok; all three parameters are argued 
auto val2 = alternative_sum<long long, int, long> (i, long);
```

```
template <typename T> 
T sum(int i, int j){ 
return i + j;
}

auto val = sum<long>(10, 20); val is long
auto val2 = sum<int>(100, 10); val2 is int
```

The idea is declaring the return type using the explicit template argument.
##### Normal Conversions are Applied for Explicitly Specified Arguments
The idea here is that even if I took my example from above and did something insidious like this: 
```
return (long)(i + j);

auto val = sum<int>(100, 10); // val is an int still
```
There is a conversion, as we have specified what type the return type should be, we have instantiated the function, and then the code just follows: 
```
int sum(int i, int j)
{ 
	return (long)(i + j);
}
```
Here the `long(i+j)` is redundant, but essentially we are showing conversions;
```
template <typename T> 
T sum(int i, int j)
{ 
	double val = 3.14 + i + j;
	return val; // still will return T type, conversion 
	// will still occurr as normal
}
```

This is true of the input of the arguments as well, they will be converted to whatever we have explicitly asked for: 
```
long lng; 
compare(lng, 1024);// Error, not of same type
compare<long>(lng, 1024); // fine instantiates compare(long,long), will convert whateveqr it needs to 
compare<int>(lng, 1024); // compare(int, int)
```

#### Trailing Return Types and Type Transformation
The above works well when we want to let the user define what the return should be. 
If the return type is really odd, then what do we write, do we want the user to have to define that themselves, particular if the function doesn't do what they think it might etc. 
Here for instance: 
```
template <typename It> 
??? & fcn(It beg, It end)
{ 
	// process the range 
	return *beg; // return a reference to an element from the range
}
```

A trailing return type would be the guy, as that means that we can define the return type after we have argued the parameters' types. 

```
// a trailing return lets us declare the return type after the parameter list is seen 
template<typename It> 
auto fcn(It beg, It end) -> decltype(*beg)
{ 
	// process the range
	return *beg; // returning a reference to an element from the range
}
```
#### The Type Transformation Library Template Classes
#typetransformation
Sometimes we don't have direct access to the type that we need. 
If we know close to nothing about the types that we are passing in. 
Using the library **type transformation** template. 
They are defined in the `type_traits` header. 
The classes in `type_traits` are used for so-called template metaprogramming. 
#typetraits

![[Pasted image 20240517001435.png]]

In this case we can use `remove_reference` in order to obtain the element type. Very cool indeed. 
If we instantiate `remove_reference<int&>` the `type` member will be `int`. 
`remove_reference<string&>` type will be `string`. 
`remove_reference<decltype(*beg)>::type` will be the type that the beg iterator points to. 
Remember that `decltype(*beg)` will return a reference type of the element type. 

The rewritten version will look something like this: 
```
// must use typename to use a type member of a template parameter

template <typename It> 
auto fcn2(It beg, It end)->
	typename remove_reference<decltype(*beg)>::type
{ 
	// process the range
	return *beg;
}
```

`type` in this sense is a member of a class, that depends fully on the template parameter. 
As a result, we must use `typename` in order to declare that it is in fact a type. 
Each template from the list earlier, has a `public` member named `type` that represents the type. 
That type may be related to the template's own template type parameter, in a way that is indicated by the template's name. 



#### My Own Notes: 
```
template <typename T> 
void foo(T arg){ /*...*/ }
foo(5); // deduced to int
foo(3.14); // deduced to double
foo("bar"); // deduced to const char*
```

```
template <typename T> 
void foo(const T&) { /*...*/ }

int x = 5; foo(x); // T = int
const double y = 3.14; foo(y); // T = double
const char* z = "bar"; foo(z); // T = const char*
```

[CPP Ref of this](https://en.cppreference.com/w/cpp/language/template_argument_deduction)

You wouldn't be able to do something like this: 
`f({1, "abc"})` and pass that into something like this: 
```
template <typename T> 
void f(std::intialize_list<T>);
// each element of the list is deduced to soemthing different
```
```
template <class T, int N> 
void h(T const(&)[N]);
h({1,2,3}); // T = int, N = 3

template <class T> 
void j(T const(&)[3]);
j({42});// array bound is not a parameter, not considered

template <int M, int N> 
void m(int const(&)[M][N]);
m({{1,2}, {3,4}}); // M = 2, N = 2
```

```
template <class T> 
int f(T(*p)(T));

int g(int);
int g(char);

f(g); // here the only overload that works is the int
// as T remains consistent there

// here
// p = T(*)(T) 
// only finding one overload that works is what we want here 
```

```
template <class T> 
void f(T);

// A = Argument
// P = Param
// T is type deduced

int a[3]; 
f(a); // Param = T, A = int[3], decays to int*, T = int*

void b(int);
f(b); // A = void(int), function type, decays to pointer
// T = void(*)(int)

const int c = 133; 
f(c); // A = const int, deduced T = int
```

If P is a reference type, then we will use it for deduction. 

#### Function Pointers and Argument Deduction
Assuming we have a function pointer that points to a function returning an `int` taking in two parameters, each of which is a reference to a `const int`. 
We can use that pointer to point to an instantiation of `compare`: 
```
template <typename T> int compare(const T&, const T&);
// pf1 points to the instantiation in compoare(const int&, const int&)
int (*pf1)(const int&, const int&) = compare;
```

The type of parameters in `pf1` determines the type of the template argument for `T`. 
The template argument for T is `int`. 
Therefore the `pf1` points to the instantiation of `compare` with T bound ot `int`. 
**It would be an error if the template arguments cannot be deteremined from the function pointer type**: 
```
// overloaded version of func; each takes a different function pointer type
void func(int(*)(const string&, const string&));
void func(int(*)const int&, const int&);
// functions taking in functions
func(compare); // error which instantiation of compare?;
```

The problem is that by looking at the type of `func's` parameter, we can't determine a unique type for the template argument. 
The call to func can take either argument here, which one do we instantiate?

If we want to disambiguate, by using explicit template arguments: 
```
// this next is fine, we specify which one we are using, therefore which one to instantiate
func(compare<int>); // 
// will pass compare(const int&, const int&);
```

In essence we have to allow the context of the call to clearly define which type of a function we want to instantiate. 

#### Template Argument Deduction and References
`template <typename T> void f(T &p);`
Normal reference binding rules apply; and `consts` are low level, not top level.

Just read the "Modern C++" book again lol.

### Type Deduction from Lvalue Reference Function Parameters
Say our parameter has the form `(T&)`: here we can only pass an lvalue in, the arg might not be a `const` type; If the argument is const then `T` will be deduced as a `const` type. 

```
template <typename T> void f1(T&);// argument must be an lvalue
// calls to f1 use the referred-to-type of the argument as the template parameter type

f1(i); // is is an int; template parameter T is int
f1(ci); // ci is a const int; template parameter T is const int
f1(5); // error as argument passed cannot be bound to lvalue ref
```

If a function parameter has type `const T&`, normal binding says that we can pass any kind of argument - an object (`const` or otherwise) - in "Modern C++" this is explained as a universal reference.
When the function parameter is itself `const`, the type deduced for `T` will not be a `const` type, as we have announced const in the parameter. We can't instantiate it to `const const int&`. Therefore `T` just becomes a `int`. 

```
template <typename T> void f2(const T&); // can take an rvalue
// parameter in f2 is const&; const in the argument is irrelevant

// in each of these calls, f2's function parameter is inferred as const int&
f2(i); // i is an int; template parameter T is int
f2(ci); // ci is a const int, T will be an int 
f2(5); // T becomes an int here, makes sense
```

#### Type Deduction from Rvalue Reference Function Parameters
If we have the form `T&&`, normal binding rules will say that we can pass an rvalue to this parameter. 
It acts pretty normally: 
```
template <typename T> void f3(T&&);
f3(42); // argument is an rvalue of type int; template parameter T is int
```
#### Reference Collapsing and Rvalue Reference Parameters
Assuming `i` is an `int` object, then calling `f3(i)` would be thought of as illegal, normally we casn't bind an rvalue reference to and lvalue.
HOWEVER, look in the "modern C++ book", you'll see that the type deduction means that this is a "universal reference". 

When we pass an lvalue (eg. `i`) to a function parameter, that is an rvalue reference (within the parameters) to a template type parameter (eg. `T&&`), the compiler deduces the template type parameter as the argument's lvalue reference type. So when we call `f3(i)`, the compiler will deduce the type of `T` as `int&`, not `int`. 

Deducing `T` as `int&` would seem to mean that `f3`'s function parameter would be an rvalue reference to the type `int&`. 
Ordinarily, we can't define a reference to a reference. 
However, it's possible to do so indirectly through a type alias, or through a template type parameter: 

Another exception is when we create a reference to a reference, then those references "collapse". 
In all but one case, the references collapse to form an ordinary lvalue reference type. 
#referencecollapsing
This will now include rvalue references too. 
```
X& &, X& &&, X&& & all collapse to type X& 

the type X&& && collapses to X&&
```

> Reference collapsing applies only when a reference to a reference is created indirectly, such as in a type alias or a template parameter

The combination of the reference collapsin rule and the special rule for type deduction for rvalue reference parameters means that we can call `f3` on an `lvalue`. 
Just remember the idea of a universal reference here.
When we pass an lvlaue to `f3`'s (rvalue reference) function parameter, the compiler will deduce `T` as an lvalue reference type: 
```
f3(i); // argument is an lvalue, template parameter T is int&
f3(ci); // arg is an lvalue, template parameter T is const int&
```

The important point here is that when the **template parameter** `T` is deduced as a reference type, the collapsing rule says that the **function parameter** `T&&` collapses to an lvalue reference type. 
The idea is this: 
```
// invalid code, for illustration purposes only
void f3<int&>(int& &&); 
// when T is int&, function paramer is int& &&
```

The function parameter in `f3` is `T&&` and `T` is `int&`, the whole thing collapses to `int&`. 
This is only true of type deduction, due to the collapsing rule.
#collapsingrule

The end product is instantiated as such: 
`void f3<int&>(int&);` 
Due to function reference collapsing. 

There are two important consequences form these rules: 
- A function parameter that is an rvalue reference to a template type parameter (`T&&`) can be bound to an lvalue; and
- If the argument is an lvalue, then the deduced template argument type will be an lvalue reference type and the function parameter will be instantiated as an (ordinary) lvalue reference parameter (`T&`). 

Like with the "universal reference" idea, we can pass any type of argument to a `T&&` function parameter. 
They can be used as lvalues and they can be used as rvalues too. 

They just ass on whatever they are given, give them an lvalue, they pull into the function an lvalue, give them an rvalue, they pull into the function an rvalue. 

#### Writing Template Functions with Rvalue Reference Parameters
```
template <typename T> void f3(T&& val)
{ 
	T t = val; // copy or binding a reference?
	t = fcn(t); // does the assignment change only t or val and t?
	if(val == t) { /* ... */ }// always true if T is a reference type
}
```
Calling `f3` on an rvalue, such as the literal 42, T is `int`. Therefore, `t` is an `int` as well, and is initialized by copying the value of the parameter `val`. 
Assigning to `t` the parameter `val` remains unchanged. 
However, imagine if `t` is a `int&`, then `t` is a reference/synonym for `val`. They, are then bound. 
When we then bind to `t` with `t=fcn(t)`, if `t` is a reference, then `val` will be changed as well. 

In the "Modern C++", we use `std::forward` in order to make sure that we passing in, what we got in, an rvalue, or an lvalue, depending on what we put into `std::foward`, however, `std::move` will always arg an rvalue reference. 

Most of the time, when we use this idea of rvaleu reference parameters, we are looking to do two things; foward the arguments, or the template is overloaded. 

It's worth noting that function templates that use rvalue references often use overloading in the same way as we saw. 

```
template <typename T> void f(T&&); // binding to anything non const
template <typename T> void f(const T&); // lvalues and const rvalues
```


