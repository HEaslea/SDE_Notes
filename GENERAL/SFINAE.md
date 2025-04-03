### Substitution Failure is not an Error
When we begin to instantiate some function templates, the compiler first has to determine what template arguments should be used for that candidate, then substitute in those arguments in the function parameter list and in its return type. 
Then it evaluates how well it matches (just like an ordinary function). 

We can run into some problems, meaning that our constructs that make no sense. 
We might produce some constructs that make no sense, and we can use that. 

Candidates that have this substitution error, are simply ignored. 
They do not cause errors during the compilation process, which would mean that we would have to make sure that every template of everything, has to work with every instantiation and every type that we feed into them. 

At the very end, we need to select the most appropriate template based on type traits or other compile time conditions. 

We have the template, then we want to substitute arguments given into the template. 
If we encounter any invalid syntax or types. 
Perhaps we have something along the lines of `T->varMember` where `T` is not a pointer type. 
Then **SFINAE** will determine that this particular substitution path is invalid. 

#### Enabling Functions for Specific Types: 

```
#include <type_traits>

// this is enabled only for integral types
template<typename T> 
typename std::enable_if<std::is_integral<T>::value, void>::type
print(T value)
{ 
	std::cout << "Integral: " << value << std::endl;
}

tempalte <typename T> 
typename std::enable_if<std::is_floating_point<T>::value, void>::type
print(T value)
{
	std::cout << "Floating Point: " << value << std::endl;
}
```

Here `std::enable_if`  is in the return types, to make sure that we enable or disable the function, however, what if we wanted to return a type: 

In `std::enable_if`, if the conditional is true, then it will define a type alias, however, if false, then it won't. 

```
template <typename T> 
typename std::enable_if<std::is_integral<T>::value, T>::type
increment(T value)
{ 
	return value + 1;
}
```
This does work, if you see that `type` is just the type that we pumped in, **if the enable_if** does in fact meet the condition. 

#### Alternatives
Keeping the return type clean, by putting `std::enable_if` in the template itself. 

```
template <typename T, typename = std::enable_if<std::is_integral<T>::value>>
T increment(T value)
{ 
	return value + 1;
}
```

You can even put it in the defaulted function parameter: 
```
template <typename T> 
T increment(T value, std::enable_if<std::is_integral<T>::value, int> = 0)
{ 
	return value + 1; // notice that the second parameter is ignored here
}
```

Another way is to use `constexpr if`
```
template <typename T> 
T increment (T value)
{ 
	if constexpr (std::is_integral_v<T>)
	{ 
		return value + 1;
	} else 
	{ 
		static_assert(std::is_integral_v<T>, "Type must be integral");
	}
}
```

Then later on, in C++ 20 There are concepts: 
```
template <typename T> 
concept Integral = std::is_integral_v<T>;

// this is so much nicer lol
Integral auto increment(Integral auto value) -> Integral decltype(value)
{ 
	return value + 1;
}
```

#### Best Practices
Prefer Template Parameter Constraints: This is often the cleanest way of doing things, and the most readable to those wanting to decode what we've written. 
Get on top of concepts as soon as we can. 

#### How Do we Disable Templates?
###### How does `std::enable_if` Disable Anything
```
template <bool B, class T = void> 
struct enable_if {};

template <class T> 
struct enable_if<true, T> { using type = T; };
```

When `B` is `true`, our struct `enable_if` has as member type , `type` which is an alias for `T`. 

When `B` if `false`, our struct `enable_if` does not define type, `type`. This absence of `type` causes SFINAE to kick in when `std::enable_if` is used. 

We haven't defined something her, that would need to be defined in order for that template to work. 

So when we write something like this; putting `enable_if` in the return type here: 
```
template <typename T> 
typname std::enable_if<std::is_integral<T>::value, T>::type
increment (T value)
{ 
	return value + 1;
}
```

Here, we are saying that if `std::is_integral<T>::value` is `true`, then `type` is our`T` that we passed in. 
(BTW this works for Char as well, as it is an integral type).

Here if `T` is not integral, then `std::enable_if<false, T>::type` is what is substituted here, however, it does not exist. 

```
template <typename T> 
typename std::enable_if<std::is_floating_pont<T>::value, T>::type
multiply_by_two(T value)
{ 
	return value * 2;
}
```


Remember this: 
```
template <typename T, typename Enable = void> 
struct is_special {};
```
Remember that this is generic one, this is one that will be instantiated, when we don't give the second argument to the template. 

```
template<typename T, typename E = void>
struct isSpec
{
    void OP()
    {
        std::cout << "OP DEFAULT SECOND PARAMETER" << std::endl;
    }
};

template <typename T>
struct isSpec<T, int>
{
    void OP()
    {
        std::cout << "OP WITH INT AS SECOND" << std::endl;
    }
};
```

Here's a cool way of looking at `std::enable_if`: 
```
template <typename T> 
class has_serialize
{ 
	template <typename U> 
	static auto test(int) -> decltype(std::declval<U>().seralize(), std::true_type());

	template <typename> 
	static std::false_type test(...);

public: 
	static constexpr bool value = decltype(test<T>(0))::value;
};

// generic save function if T has seralize()
template <typename T> 
typename std::enable_if<has_serialize<T>::value, void>::type
save(const T& obj)
{
	obj.serialize();
} 

struct Serializable
{ 
	void serialize() const 
	{ 
		...
	}
};

struct NonSerializable {};
int main() 
{ 
	Serializable s; 
	NonSerializable ns;
	save(s); // this is fine

	save(ns); // giving a compilation error, no matching function
}
```

Overloading Class Template Specializations: 
```
// primary template
template <typename T, typename Enable = void> 
struct Wrapper;

template<typename T> 
struct Wrapper <T, typename std::enable_if<!std::is_pointer<T>::value>::type> 
{ 
	void info() const 
	{ 
		std::cout << "Wrapper for non-pointer type" << std::endl;
	}
}

// specialization for pointer types
template <typename T> 
struct Wrapper <T, typename std::enable_if<std::is_pointer<T>::value>::type> 
{ 
	void info() const 
	{ 
		std::cout << "Wrapper for pointer type" << std::endl;
	}
}
```

### Conditional Inheritance

```
// base class
struct Debuggable
{ 
	void debug() const 
	{ 
		// .... 
	}
};

// base primary template, given T and default secondary to void
template <typename T, typename Enable = void> 
struct MyClass; 

// here we only inherit from debuggable 
// if the T that we pass in is integral 
template <typename T> 
struct MyClass<T, typename std::enable_if<std::is_integral<T>::value>::type> : 
	Debuggable
{ };

// here we are not inheriting, as T is not an integral type
template <typename T> 
struct MyClass<T, typename std::enable_if<!std::is_integral<T>::value>::type>
{ };
```


**Creating a Generic `min` Fucntion**
```
template <typenaem T, typename U = T> // if not given second, then U = T
struct is_less_than_comparable
{ 
private: 
	template<typename X, typename Y> 
	static auot test(int) -> decltype(std::declval<X>() 
		<std::declval<Y>(), std::true_type());

	template<typename, typename> 
	static std::false_type test(...);

public: 
	static constexpr bool value = decltype(test<T, U>(0))::value;
};


// generic min function enable only if T is less-than comparable
template <typename T> 
typename std::enable_if<is_less_than_comparable<T>::value, T>::type
min(const T& a, const T& b)
{ 
	return (a < b) ? a : b;
}

struct Comparable
{ 
	int value;
	
	bool operator<(const Comparable& other) const 
	{ 
		return value < other.value;
	}
}

struct NonComparable
{ 
	int data;
};
``` 



#declval
### `std::declval` in Checking For Member Functions of a Type
`std::declval` allows you to create a reference to a type (including non default constructible types) without actually constructing an object fo that type. 
Very useful when we want to check the properties of an object, without creating an instance of them. 

From CPPref : 
Converts any type T to a reference type, making it possible to use member function in the operand of the `decltype` specifier without the need to go through constructors. 

Possible Implementation:
```
template <typename T> 
typeanem std::add_rvalue_reference<T>::type declval() noexcept
{ 
	static_assert(false, "declval not allowed in an evaluated context");
}
```
Example:
```
struct Default
{ 
	itn foo() const { return 1; }
}

struct NonDefault
{ 
	NonDefault() = delete;
	int foo() const { return 1; }
};

int main() 
{ 
	decltype(Default().foo()) n1 = 1;
	
	// error as no default constructor
	// decltype(NonDefault().foo()) n2 = 1;

	decltype(std::declval<NonDefault>().foo()) n2 = n1 // type of n2 is int
}
```

Using to check for a member function then looks something like this: 
```
template <typename, typename = void> 
struct has_foo : std::false_type {}; // inheriting from false_type

tempalte <typename T> 
struct has_foo<T, std::void_t<decltype(std::declval<T>().foo())>> : std::true_type {}; 

class A 
{ 
	void foo() {}
};

class B 
{ 
	void bar() {}
};

int main() { 
	std::cout << std::boolalpha;
	std::cout << "A has foo: " << has_foo<A>::value << "\n"; // Output: true
	std::cout << "B has foo: " << has_foo<B>::value << "\n"; // Output: false 
}
```
The `declval<T>()` produces an rvalue reference of T. 
Then `decltype(std::declval<T>().foo())` attempts to evaluate the expression `std::declval<T>().foo()`. 
If `T` does have `foo()` then this is a valid method, and `decltype()` will successful deduce the return type. 
Then the `void_t`, if what's in it is true, then the type returns to `void` and that's fine, and allows for `true_type` at the end. 
If it is invalid, then the template fucks off, and we use the first one that inherits from `false_type`. 

Check for a specific Signature of a Member Function
```
template <typename, typenaem T, typename = void> 
struct has_foo_with_int : std::false_type {}

template <typeanem C, typename T> 
struct has_foo_with_int<C, T, std::void<decltype(std::declval<C>().foo(std::declval<T>()))>> : 
	std::is_same<decltype<C>().foo(std::declval<T>())), void> {};
```

Here we are looking at a template that takes in two arguments. 
Taking in `C , T`. 
`std::declval<C>().foo(std::declval<T>())` is checked, what we are looking for here, is checked to see whether it returns anything. 
The `std::is_same<decltype(...), void>` ensures that the function will return void. 

### Using Decltype
We looked at whether a class or a struct has a member function in order to use that function, say this will work if `T` has the member function `T.foo()`, if it does then we know that the function can use it etc. simple stuff. 

Remember the comma operator, that it will execute, left to right, however, will only take the right most expression as the value: 
```
template <typename T> 
void funct(T&& t) -> decltype(t.foo(), void())
{ 
	// Implementation when T has a member foo. 
}

template <typename T> 
void func(...) 
{ 
	// the fallback implementation when T does not have foo
}
```
Here we use `declval` as well, and a secondary `enable_if` with the `!` in order to write a specialization that will take types that do not have `foo`. 

We start by making structs that inherit from `false_type, true_type` in order to have `::value` that correspond to `false, true` respectively: 

```
template <typename T, typename = std::void_t<>> 
struct has_foo : false_type { };

// specialization for when it does have foo()
template <typename T> 
struct has_foo<T, std::void_t<decltype(std::declval<T>().foo())>> : true_type {};

/* 
THIS IS SORT OF A LONG WAY OF DOING CONCEPTS
*/

// then we write the calling to function, that will 
template <typename T> 
std::enable_if_t<has_foo<T>::value, void> 
call_foo (T& t)
{ 
	t.foo();
}

template <typename T> 
std::enable_if_t<!has_foo<T>::value, void> 
call_foo (T& t)
{ 
	cout << "NO FOO()" << endl;
}

struct WithFoo
{ 
	void foo() { cout << "Foo been called" << endl; }
}

struct WithoutFoo
{ 
	void bar() { cout << "NO FOO" << endl; }
}

int main() 
{ 
	cout << boolalpha;

	WithFoo wf;
	WithoutFoo wof;

	call_foo(wf);
	call_foo(wof);
}
```

There is an alternative, using `decltype` directly in `std::enable_if`. 

```
template <typename T> 
auto add(const T& a, const T& b) -> decltype(a + b, T())
{ 
	return a + b;
}

template <typename T> 
void add(const T& a, const T& b)
{ 
	cout << "Addition not supported by this type" << endl;
}
```


##### Helper Classing 
```
template <typename FROM, typename TO>
struct IsConvertibleHelper
{
private:
    static void aux(TO); // going to use this to test if F -> T 
    template <typename F, typename T,
        typename = decltype(aux(std::declval<F>()))>
        static std::true_type test(void*);
    // test fallback()
    template <typename, typename>
        static std::false_type test(...) ;
public:
    using Type = decltype(test<FROM>(nullptr));
};

template <typename FROM, typename TO>
struct IsConvertibleT : IsConvertibleHelper<FROM, TO>::Type
{
};

template <typename FROM, typename TO>
using IsConvertible = typename IsConvertible<FROM, TO>::Type;

template <typename FROM, typename TO>
constexpr bool isConvertible = IsConvertible<FROM, TO>::value;
```
The primary goal is to implement a **type trait** to check type convertibility at compile time. 
The result being `std::true_type` or `std::false_type`, indicating `FROM` and `TO` are convertible. 

The `aux(TO)` is a function that takes a parameter of type `TO`. 
```
template <typename F, typename T, 
	typename = decltype(aux(std::declval<F>()))> 
static std::true_type test(void*);
```

We defined the `aux(TO)` in the earlier part of the class. 
Then when we do `decltype(aux(std::declval<F>())` means that we are passing a type of `F` in here, in order to check if this is valid. 
If it is valid, then we **RETURN** `std::true_type` this means that we can `decltype` of that function in order to see if we have a true type or not. 
`decltype` on functions will test the return type. 

```
int foo() { return 42; }
decltype(foo()) x; // this will be of int
```

Then the fall back is something along the lines of: 
```
tmeplate <typename, typename> 
	static std::false_type test(...);
```
This will match anything. 

Then we have the result type: 
`using Type = decltype(test<FROM>(nullptr));` 
This means that `Type` will be either `true_type` or `false_type`. 

Then it's more clear: 
```
template <typename FROM, typename TO> 
struct IsConvertibleT : IsConvertibleHelper<FROM, TO>::Type
{};
```

Here we are inheriting from either `true_type` and `false_type`. 
Then we can just use a Template Alias: 
```
template <typename FROM, typename TO> 
using IsConvertible = typename IsConvertible<FROM, TO>::Type;
```

```
template <typename FROM, typename TO> 
constexpr bool isConvertible = IsConvertible<FROM, TO>::value;
```
this `::value`  is elusive, it's from `true_type` and `false_type`. 
The inner template is required here. 

The outer template parameterized with `FROm` and `TO`. 
**REMEMBER THIS VERY IMPORTANT FACT**:
**SFINAE ONLY WORKS WITH FUNCTION TEMPLATES OR ALIAS TMEPLATES, NOT WITH CLASS TEMPLATES**. 
This is mostly because outer class templates, will not be able to provide "fallbacks" based on SFINAE. 

Rather than SFINAE for class templates, what we are looking at is using `static_assert` to poison invalid types. 
```
template <typename T> 
class My_float
{ 
	static_assert(std::is_floating_point<T>::value);
};
```

```
template <typename T, bool = std::is_floating_point<T>::value> // providing the parameters
class my_float; 

template <typename T> class my_float<T, true> { /* ... */ };
```

Remember the idea that when we have a template specialization like this: we only need to say what values we have to argue for, in terms of parameters: meaning, in the second case, that we are just asking for `T`, we don't mind what that is. 
HOWEVER, we are also saying that, considering the first `my_float`, we want the second argument to be true, we aren't taking that in, and leaving it up to the calling, it's already decided. 

Like that chat. 
HOWEVER, **class templates can be specialized, unlike function templates**. 

