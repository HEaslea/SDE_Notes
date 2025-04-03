#avoidambiguousconversions
If there are one or more conversions, in a class, it's important that we ensure that there is only one way to convert from the class type to the target type. 

If there is more than one way, then we will find it hard to write unambiguous code. 

There are two ways that multiple conversion paths can occur. 

We might have the issue: a var of type A is to be converted to type B; A has a conversion to B, and B has a type conversion taking a type A. 

The other is that we define multiple conversions from or to types that are themselves relate by conversion. The most obvious is the built-in arithmetic types. A given class should normally define at most one conversion to or from an arithmetic type. 

> Ordinarily it is a silly idea to define classes with mutual conversion or to define conversion to or from two arithmetic types. 

We really just want to avoid conversions that are interlocked and webbed between each other. 

##### Argument Matching and Mutual Conversions
Here we are defining two ways to obtain an A from a B: 
either by using B's conversion operator o by using the A constructor that takes a B: 

```
// usually a bad idea to have mutual convresion between two class types 
struct B; 
struct A{ 
	A() = default;
	A (const B&); // converts a B to an A
	// other members
};

struct B{ 
	operator A() const; // also converts a B to an A
	// other members
};

A f(const A&); 
B b;
A a = f(b); // error ambiguous : f(B::operator A())
// or f(A::A(const B&))
```

There are two ways to obtain A from a B. 
The compiler won't know which one to call, idiot. 
Both are equally as good as another. If we wanted to make this call as standing: 
```
A a1 = f(b.operator A()); // Ok use B's conversion operator
A a2 = f(A(b)); // Ok : explicitly using A's constructor
```

Using a cast would cause the same ambiguity. 

##### Ambiguities and Multiple Conversion to Built-in Types
Ambiguities may also occur, when a class defines multiple conversion to (or from) types that are themselves related by conversions. 
When a class defines constructors from or conversion to more than one arithmetic type. 

eg. 
The following class has converting constructors from two different arithmetic types, and conversion operators to two different arithmetic types: 
```
struct A { 
	A(int = 0); // usually a bad idea to have two
	A(double); // conversion from arithmetic type

	operator int() const; // usually a bad idea to have two
	operator double() const; // conversion to arithmetic type
// other members
};

void f2(long double);

A a;

f2(a) // error ambiguous call to f(A::operator(int))
// f(A::operator double())

logn lg; 
A a2(lg); // Error ambiguous call A::A(int) or A::A(double)
```

In the call to `f2`, neither conversion is an exact match, to a `long double`. 
However, either conversion can be used, followed by a standard conversion to get to `long double`. 
Neither conversion therefore is better than the other; the call is ambiguous. 

We encounter the same problem when we init `a2` from a `long`. 

Each would require that the arg be converted before using the constructor: 
- Standard `long` to `double` conversion followed by `A(double)`. 
- Standard `long` to `int` conversion followed by `A(int)`. 
These are indistinguishable, therefore ambiguous. 

```
short s = 42; 
// promoting short to int is better than converting short to double 
A a3(s); // uses A::A(int)
```
Promoting a `short` to `int` is preferred to converting the `short` to a `double`. 
Hence `a3` is constructed using `A::A(int)` constructor, from promoted `s`. 

> The compiler will use rand of the standard conversion, *preceding* or *following* the conversion, to select the best match. 


###### Overloaded Functions and Converting Constructors
Choosing among multiple conversions is further complicated when we call and overloaded function. 
If two or more conversion provide a viable match, then the conversions are considered equally good. 

Essentially overloading functions, where there is some ambiguity to Converting Constructors, or Conversions in general.

There can be some issues and errors created if we are not careful with overloaded operators, conversion constructors, and conversion functions. 
Some rules of thumb: 
- **Don't define mutually converting classes** - if class `Foo` has a constructor that takes an object of class `Bar`, do not give `Bar` a conversion operator to type `Foo`. 
- Avoid conversions to the built-in arithmetic types. In particular, if you do define a conversion to an arithmetic type: 
	- Do not define overloaded versions of the operators that take arithmetic types. If users need to use these operators, the conversion operation will convert objects of your type, and then the built-in operators can be used. 
	- Do not define a conversion to more than one arithmetic type. Let the standard conversions provide conversions to the other arithmetic types. 

The easiest rule of all: With the exception of an `explicit` conversion to `bool`, avoid defining conversion functions and limit non`explicit` constructors to those that are "obviously right". 

```
struct C{ 
	C(int); // other members
};

struct D{ 
	D(int); // other member
};

void manip(const C&);

void manip(const D&);

manip(10); // which manip do we use
// both can be called as const T& can capture Rvalue Ref
```

They would both convert the `int` into `C` or `D`. 
Just remember that idea that we shouldn't be casting or using a constructor in an argument to an overloaded function. Bad design. 

###### Overloaded Functions and User-Defined Conversion
In a call to an overloaded function, if two (or more) user-defined conversions provide a viable match, the conversions are considered equally good. 

Whether a built-in conversion is also needed is considered only if the overload set can be matched *using the same conversion function*. 

eg. 
Our call to `manip` would be ambiguous even if one of the classes defined a constructor that required a standard conversion for the argument: 
```
struct E{ 
	E(double);
	// other members
};

void manip2(const C&); 
void manip2(const E&);

// this will be erroneous: two different user-defined conversions could be used
manip2(10); // manip2(C(10)) or manip2(E(double(10)))
```

Here, `C` has a conversion from `int` and `E` has a conversion from `double`. 
The call `manip2(10)`, both `manip2` functions are viable: 
- `manip2(const C&)` is viable because C has a converting constructor that takes an `int`. That constructor is a exact match for the argument. 
- `manip2(const E&)` is viable because `E` ahs a converting constructor that takes a `double` and we can use a standard conversion to convert the `int` argument in order to use that converting constructor. 
Calls to overloaded functions require *different* user-defined conversions from one another, this call is ambiguous. 
Even though one seems to be a perfect match (which it is), the other requires a standard conversion, this will still flag in error. 

In a call to an overloaded function, the rank of an additional standard conversion (if any) matters only if the viable functions require the same user-defined conversion. 


[Implicit Conversion Sequences](https://www.ibm.com/docs/en/i/7.4?topic=only-implicit-conversion-sequences-c)

A sequence of conversions required to convert an argument in a function call to the type of the corresponding parameter in a function declaration.

The compiler will categorize each implicit conversion sequence in one of three categories. 
- [Standard Conversion Sequences](https://www.ibm.com/docs/en/i/7.4?topic=only-standard-conversion-sequences#standard_conv_sequence)
- [User-defined conversion sequences](https://www.ibm.com/docs/en/i/7.4?topic=only-user-defined-conversion-sequences)
- Ellipsis conversion sequences - weird, not useful atm.


**Standard Conversion Sequences**: 
![[Pasted image 20240430115130.png]]

**User-Defined Conversion Sequences**: 
![[Pasted image 20240430115319.png]]

[User-Defined Conversion Function](https://en.cppreference.com/w/cpp/language/cast_operator)

If both conversion functions and converting constructors can be used to perform some user-defined conversion, the conversion functions and constructors are both considered by overload resolution in copy-initialization and reference-initialization contexts, <u>but</u> only the constructors are considered in direct-initialization contexts. 

```
struct To{ 
	To() = default;
	To(const struct From&) {} // converting constructor
	// taking From&
};

struct From{ 
	operator To() const { return To(); } // conversion function
	// returning a To object 
};

int main()
{ 
	From f;
	To tl(f); // direct-initialization: calls the ctor
	// IF converting ctor is not available, implicit copy constructor will be selected, and conversion function will be called to prepare its argument

	// To t2 = f; // copy-initialization : ambiguous
	// Note if conversion function is from a non-const type
	// From::operator To(); 
	// it will be selected instead of the ctor in this case
	
	To t3 = static_cast<To>(f);
	// direct-initialization calls the conversion ctor

	const To& r = f;
	// reference-initialization : ambiguous
}
```
The last is ambiguous due to multiple possible conversions available: 
Do we use the converting constructor of To (that takes a From object), or The conversion function of From, that converts to a To. 
The `static_cast<To>(f);` and direct initialization result in unambiguous conversions because they specifically invoke the converting constructor of `To`. 
Copy-initialization and Reference-initialization can make this process ambiguous. 

```
struct D;
 
struct B
{
    virtual operator D() = 0;
};
 
struct D : B
{
    operator D() override { return D(); }
    // Creates a new obj of D, returns it
};
 
int main()
{
    D obj;
    D obj2 = obj; // does not call D::operator D()
    B& br = obj;
    D obj3 = br;  // calls D::operator D() through virtual dispatch
}
```
So `D obj2 = obj` doesn't invoke `D::operator D()` as `obj` is already a `D` object. 
`B& br = obj;` As `D` is derived from `B` this is allowed. 
`D obj3 = br;` Remember that `br` is of type `B`, therefore we need the conversion operator `D::operator D()` to convert it here. 

`static_cast<>` Can use either converting constructors or conversion functions, depending on the types involved and the context of the conversion. 

![[Pasted image 20240430124413.png]]

[Cherno Video about Converting](https://www.youtube.com/watch?v=OK0G4cmeX-I&t=698s)

You can prevent conversions: 
```
class O{
public: 
	operator float() = delete;

	operator int() { return 4; }
};

void PrintFloat(float &f){
	 cout << f << endl;
}

void PrintInt(int i){ 
	cout << i << endl;
}

int main(){ 
	O o; 
	PrintInt(o); // fine well-defined int conversion
	PrintFloat(o); // Error, no conversion possible
}
```

A pointer is just an integer, and a boolean is just an integer as well: 
`if (!pointer)` is fine. 

##### Function Matching and Overloaded Operators
#functionmatchingoverloadedoperators
Overloaded Operators are just overloaded Functions. 

Normal function matching is used to determine which operator - built-in or overloaded  - to apply to a given expression. 
When an operator function is used in an expression, the set of candidate functions is broader than when we call a function using the call operator. 

If `a` has a class type, the expression `a sym b` might be
```
// sym is just a stand-in for symbol
a.operatorsym(b); // a has operatorsym as a member function
operatorsym(a,b); // operatorsym is an ordinary function
```

Unlike ordinary calls, we can't use the form of the call to distinguish whether we're calling a nonmember or a member function. 

When we use an overloaded operator with an operand of class type, the candidate functions include ordinary nonmember versions of that operator, as well as built-in versions of the operator. 

If the LHO has class type, the overloaded versions of the operator, if any, defined by that class are also included. 

When calling a named function, member and nonmember functions with the same name **do not** overload one another. 
This is as the way that we call member functions distinguishes itself between non member functions. 

When a call is through an object of class type, then only the member functions of that class are considered. 
When we use an overloaded operator in an expression, there is nothing to indicate whether we're using member or nonmember functions. 

Therefore we have to consider both. 


> The set of candidate functions for an operator used in an expression can contain both nonmember and member functions. 

Here's the example we were waiting for:

```
class SmallInt{ 
	friend
	SmallInt operator+(const SmallInt&, const SmallInt&);
public: 
	SmallInt(int = 0); // conversion from int, with default
	operator int() const { return val; } // to int conversion

private: 
	std::size_t val;
};
```

We can use this class to add two `SmallInt`s, but we might run into ambiguity here as we have a conversion to int, and then the compiler might just use the built-in `int + int` operator. 

```
SmallInt s1,s2;
SmallInt s3 = s1 + s2; // uses overloaded operator +
int i = s3 + 0; // Error due to ambiguity
```

The first addition will just use the overloaded version of `+` that takes two `SmallInt`, as that is the closest match that we can get. 

The second is ambiguous, because we can convert 0 to a `SmallInt` and use the `SmallInt` version of `+`, or we just convert `s3` to an int using the `operator int() const`. 

> WARNING providing both conversion functions to an arithmetic type and overloaded operators for the same class type may lead to ambiguities between the overloaded operators and the built-in operators. 

Remember top level `const` means the thing itself is `const`, low level `const` means the thing that we are pointing to is `const`. 
`const int ci = 42;` this is top level const, the thing itself is `const`

`const int * ptr = &ci;` // pointer to a `const int`
This is low level const. 


```
class T{ 
public: 
	T(int a) : value(a) {cout << "Converted from int" << endl;}
	T() : T(0) {} // default constructor

	operator int() { cout << "Called Int Conversion" << endl;
	return value;
	}
	T operator+(const T& other){ 
		return value + other.value;
	}
	
private: 
	int value;
};

int main(){ 
	T sI = 3; // "Converted from int"
	cout << sI << endl; // automatically converted to Int
	int i = sI + 0; // ERROR
	// Ambiguity here

	// To absolve any ambiguity
	// We can ask specifically to convert sI to int 
	// then the compiler knows to use built-in + operator
	int i = sI.operator int() + 50;

	// yes, that is the syntax
}
```




