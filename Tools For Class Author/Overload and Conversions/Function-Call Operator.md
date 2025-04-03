Classes that overload the call operator, allow the objects to be used as if they were a function themselves. 

As classes can also store state, they can be more flexible than ordinary functions. 

Imagine that you were writing a function that had a bunch of static variables, well with classes, you can have those instance variables. 

The following struct is `absInt`, has a call operator that returns the absolute value of its argument: 

```
struct absInt{ 
	int operator()(int val) const { 
		return val < 0? -val : val;
	}
};
```

We have a single operation; The function call operator. 
Taking type `int` args. Returning the abs version of that int. 

Usage looks like this: 
```
itn i = -42;
absInt absObj; // object with the function call
int ui = absObj(i); // passes i to absObj.operator();
```
This has to be a member function, duh, as the object itself is making the call. 
We may have multiple versions, depending on our arguments, number and type etc. 

These are known as **function objects**. They act like function, they can be called, which is cool. 
This is pretty much what a lambda is. 

##### Function-Object Classes with State
Since we can have data member variables, we have extra variables that we can use within the Function Object. 

```
class PrintString{ 
public: 
	PrintString(ostream &o = cout, char c = '');
	: os(o), sep(c) {}

	void operator()(const string& s) const { os << s << sep;}

private: 
	ostream &os; // stream on which to writ 
	char sep; // char to print after each output
};
```


```
class Counter{ 
private: 
	int count;
public: 
	Counter() : count(0) {} // Ctor to init count

	void operator()(){ 
		++count; // Inc the count
		std::cout << "Count: " << count << std::endl;
	}
};
```
It's like having a private static variable for a function, so a non-global static variable. 

Function objects are most used as args to the algorithms. 

Eg. we can pass `PrintString` into  `for_each`. 
eg. 
```
for_each(vs.begin(), vs.end(), PrintString(cerr, '/'));
```

The 3rd argument is the temp object of type `PrintString`. 


#### Lambdas Are Function Object
When writing a lambda, the compiler translates that expression into an unnamed object of an unnamed class. 

These classes contain an overload `operator()` function-call operator. 
eg. 
```
// sort words bys ize, but maintain alphabetical order
stable_sort(words.begin(), words.end(), 
	[](const string& a, const string& b)
		{ return a.size() < b.size();});
```

Here, our lambda acts as an unnamed object of a class that might look something like this: 
```
class ShorterString{ 
public: 
	bool operator()(const string& s1, const string& s2) const
	{ 
		return s1.size() < s2.size();
	}
};
```

By default, lambdas, cannot change their captured variables ! 
Meaning that the `operator()` here is a `const` function. 
If the lambda declares itself as `mutable`, then the operator is no longer `const` duh. 

The rewritten to use the class instead would look like:
```
stable_sort(words.begin(), words.end(), ShorterString());
```

##### Classes Representing Lambdas With Captures
When we capture by reference, it is up to the program to make sure that that variable is visible when we call that lambda. 

Therefore, we can use the variable directly, without storing that reference as a data member in the generated class. 

Variables that are captured by value, are copied into the lambda. 
These classes also have a constructor to initialize tehse data members fro the value of the captured variables. 

```
// get an iterator to the first element whose size() is >= sz
auto wc = find_if(words.begin(), words.end(), 
	[sz](const string&a)) etc. 
```
Would generate that looks something like this: 

```
class SizeComp{ 
public:
	SizeComp(size_t n): sz(n) {} // param for each captured var
	// call operator with the same return type, 
	// parameters, and body as the lambda
	bool operator()(const string& s)
	 { return s.size() >= sz; }

private: 
	size_t sz; // a data member for each var captured by value
};
```

Here we have a data member, and a ctor, to initialize that data member. 

This class **does not have a default ctor** meaning that we cannot pass without any arguments. 
```
// get an it, to the first ele whose size() is >= sz
auto wc = find_if(words.begin(), words.end(), SizeComp(sz));
```
Classes generated from a lambda expression have a deleted default ctor, deleted assignment operator, and a default destructor. 



### Library-Defined Function Objects. 
#libraryfunctionobject
The STD lib defines a set of classes that represent arithmetic, relational, and logical operators. 

Each class defines a call operator, that applies the named operations. 
eg. the `plus` class has a function-call operator that applies `+` to a pair fo operands; the `modulus` class defines a call operator that applies the binary `%` operator; 
These classes are templates to which we supply a single type. 
That type specifies the parameter type for the call operator. 
eg. `plus<string>`. 
Applies the `string` addition operator to `string` objects. 
The `plus<int>` , operands have to be ints. 
`plus<Sales_data>` applies `+` to `Sales_data`s. 

```
plus<int> intAdd;  // function object that can add to int values

negate<int> intNegate; // function object that can negate an
// int value

// uses intAdd::operator(int, int) add 10 and 20 
int sum = intAdd(10, 20); // equivalent to sum = 30

sum = intNegat(intAdd(10,20)); // equiv to sum = -30;

sum = intAdd(10, intNegate(10)); // sum = 0
```

![[Pasted image 20240428015039.png]]

We can use these within the Algorithms that we learnt about: 

##### Using a Library Function Object with the Algorithms
Function-object classes that represent operators are often used to override the default operator used by an Alg. 
By default, algs use the `operator<`, which sorts into ascending order. 

To sort in descending order, we could just pass `greater` (from above). 
eg. if we have `svec`, a `vector<string>`. 

```
// passes a temporary fucniton object that applies the < operator to two strings
sort(svec.begin(), svec.end(), greater<strign>());
```
Sorts the `vector` into **descending** order. 
The third object is an unnamed object of type `greater<string>`. 
When `sort` compares elements, rather than applying the `<` operator for the element type, it will call the given `greater` function object. 

These function objects also work for pointers too.

Recall that comparing two unrelated pointers is undefined. 
We might want to `sort` a `vector` based on their address in memory. 
We would kind of have to do this, through one of the library function objects: 

```
vector<string*> nameTable; // vector of pointers
// error the pointers in nameTable are unrelated, 
// so < is undefined
sort(nameTable.begin(), nameTable.end(), 
	[](string *a, string* b) { return a < b;});
	
// ok library guarantees taht less on pointer types is well defined
sort(nameTable.begin(), nameTable.end(), less<string*>());
```


It's worth noting that the associative containers, actually use these, `less<key_type>` to order their elements. 

As a result, we can define a `set` of `pointers` or use a pointer as the key in a `map` without specifying `less` directly. 

I believe that they are in the `<functional>` header. 

##### Callable Objects and `function`
#callableobjects
There are a few kinds of callable objects: 
Functions and Pointers to functions, lambdas, objects created by `bind`, and classes that overload the function-call operator. 

These objects have a type. 
Eg. lambda has its own unique (unnamed) class type. 
Function and Function-pointer types vary by their return type and argument types, and so on. 

```
void (*)(int); // function type where return is void and one arg is int

typedef void FunctionType(int);
```

Two callable objects with different types may share the same **call signature**. 
The call signature specifies the type returned by a call to the object and the argument type(s) that must be passed in the call. 
A call signature corresponds to a function type. 
eg. 
`int(int, int)` is a function type that takes two `int`s and returns an `int`. 

#### Different Types can have the Same Call Signature
Sometimes we want to treat several callable objects that share a call signatures as if they had the same type. 
eg. Consider
```
// ordinary function
int add(int i, int j) { return i + j; }

// lambda, which generates an unnamed function-object class
auto mod = [](int i, int j) { return i % j; }

// function-object class
struct divide{ 
	int operator(){ int i, int j}
	{ 
		return i / j; 
	}
};
```

Each applies an arithmetic operation to its parameters. 
Even though they each have a distinct type, they all share the same call signature: 
`int(int, int)`

Let's define something called a **function table**, to store pointers to these callables (like from the book whoa). 
When the program needs to execute a particular operation, it will look in the table to find which function to call.

These are easy to implement using a `map`. 
So let's use a `string` corresponding to an operator symbol as the key; 
the value will be the function, the pointer to the function. 

We might define: 
```
// maps an operator to a pointer to a function taking two ints and returning an int
map<string, int(*)(int, int)> binops;

// let's put an pointer to add in binops
binops.insert({"+", add}); // adding a pair
```

However, if we wanted to add `mod` we would run into an error, `mod` is not a pointer to a function. 

`mod` is a lambda, and each one has its own class type. 
That type does not match the type of the values stored in `binops`. 

##### The Library `function` Type
#functiontype
#functionlib
We can solve this with the lib type named `function` that is defined in the `<functional>` header. 

![[Pasted image 20240429022002.png]]

`function` is of course a template. 
Just think that lib provides us with a wrapper for functions. 
We can just wrap a bunch of things with the `function` wrapper. 

So we have to supply to `function` with the signature call. 
The "functions/function objects" above all have the same signature call. 

eg.  `function<int(int, int)>`. 

This here is a function type that can represent callable objects that return an `int` result and have two `int` parameters. 
We can use that type to represent any of the functions above. 

```
function<int(int, int)> f1 = add; // function pointer
function<int(int, int)> f2 = divide(); 
// object of a function-object class

function<int(int, int)> f3 = [](int i, int j)// lambda
							{ return i * j; };

// Then we can just say

cout << f1(4,2) << endl; // prints 6
cout << f2(4,2) << endl; // prints 2
cout << f3(4,2) << endl; // prints 8
```

Now we can add our "functions" (type `function`) to our `binops` map, as "function" was a wrapper. 

`map<string, function<int(int, int)>> binops;`

So then we can just braced-initialize-list initialize this bad boy: 
```
map<string, function<int(int, int)>> binops = 
{ 
	{"+", add}, // function pointer version
	{"-", std::minus<int>()}, // lib function object
	{"/", divide()}, // user-defined function object
	{"*", [](int i, int j){ return i * j;}}, 
	// ^ unnamed lambda ^
	{"%", mod} // named lambda object
};
```

Although they all have very different object types, they can be "unionized" by lib `function`. 
In each of these, the `function` constructor can take the second of each pair. 

We can index the map using the key type: 
In doing so we get an object of type `function`. That then needs to be called. Then just pass some arguments into it
```
binops["+"](10,5);// output 15
// etc. etc. 
```

##### Overloaded Functions and `function`
We cannot (directly) store the name of an overloaded function in an object of type `function`: 
```
int add(int i, int j) { return i + j; };
Sales_data add(const Sales_data&, const Sales_data&); 
map<string, function<int(int, int)>> binops; 
binops.insert({"+", add}); // error, which add? 
```
One way here to absolve any ambiguity is to store a function pointer instead of the name of the function: 
```
int (*fp)(int, int) = add; // pointer to the version of add that takes two ints
binops.insert({"+", f;}); // ok fp points to the "right" version of add
```

Alternatively, just use a lambda: 
```
// ok to use a lambda to disambiguate which version
binops.insert({"+",[](int a, int b) { return add(a, b);}});
```
Here, the idea that we are taking in two `ints` in order to pass them on to an `add`, there is only one `add` outwardly that can take two `ints`. 

##### Wrappers
#wrappers

A construct or class that "wraps around" another object, providing a new/hopefully simplified interface. 
Used mostly to expand or adapt code without modifying it directly. 

For instance: 
[std::function<void()>](https://en.cppreference.com/w/cpp/utility/functional/function)
This wraps function objects. 


