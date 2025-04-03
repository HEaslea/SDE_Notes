We saw earlier how we can implicitly convert to a class type: 
Defining conversion **from** a class type: using a conversion operator. 

Converting Constructors and conversion operators define **class type conversion**. 

Remember that a Converting Constructor is one that takes a single argument of a different type than the class itself. 

Implicit conversion between given type and class being constructed. 

```
class MyString{ 
private: 
	std::string str;

public: 
	// converting constructor
	MyString(const char* s) : str(s) {}

	void print() const{ 
		cout << str << endl;
	}
};

// Then we can do something like this
int main(){ 
	MyString myStr = "Hello, world!";
	myStr.print(); // output hello world
}
```

The right side is implicitly converted to the object of class type, and then copied over. 

We can do this if we have more data member as well: 
```
class Test {
private: 
	int age; 
	string name;
public: 
	
	Test() : Test(0, "Default") { }
	
	Test(string n) : Test(0, n) { }

	Test(int a, string n) : age(a), name(n) { }

	Test(const char* a) : name(a) { age = 0; } 
	// this is the converting constructor
	
	int getAge() const { return age; }
	string getName() const { return name; }
};
```

The converting constructor here has to take a `const char*` as that is what a `string literal` is here: 
`Test a1 = "Hugo";` 
`"Hugo"` here is a `const char[5]` Remember the termination char. 

Ok so we are caught up to speed. 


##### Conversion Operators
#conversionoperators

`operator type() const;`

For any type other than `void` that can be a function return type. 

I can even do something like this: 

```
class Test{ 
	int age; 
	string name;
public:
	// All members the same

	operator std::vector<int>() const { 
		int size = (int)name.size(); 
		return (vector<int>{age, size});
	}
};
```

Converting an object into a vector if we wanted to. 

Conversions to an array or a function type are not permitted. 
>NO ARRAY OR FUNCTION TYPE

Conversions to pointer types - both data and function pointers - and to reference types are allowed. 

**They have no explicit return type and no parameters**. 
**They have to be defined as member functions**. 
**They should not be changing the object they are converting, so they should be `const`**. 

##### Defining a Class with a Conversion Operator

We'll define a small class that represents an integer in the range of 0 to 255; 

```
class SmallInt{ 
public: 
	SmallInt(int i = 0) : val(i)
	{ 
		if (i < 0 || i > 255)
			throw std::out_of_range("Bad SmallInt Value");
	}

	operator int() const { return val; }

private: 
	std::size_t val;
};
```
Here we are defining version to and from its type. 

```
SmallInt si; 
si = 4; // Implicitly converts 4 to SmallInt object
// then we call the SmallInt::operator=

si + 3; // Implicitly converts si to int followed by integer addition
```

As a result, we can pass any arithmetic type to the `SmallInt` constructor. 

```
// the double arg gets converted to an int 
// using built-in conversion 

SmallInt si = 3.14; // Calls the SmallInt(int) constructor

// Then SmallInt conversion operator to convert to int
si + 3.14;
// Then that int will be converted into double 
// using built-in conversio
```

Implicitly, means that we don't have to, can't really, pass arguments into that conversion: 
```
class SmallInt; 
operator int(SmallInt&); // error : nonmember

class SmallInt{ 
public: 
	int operator int() const; // error : return type
	operator int(int = 0) const; // error : parameter list
	operator int*() const { return 42; } // error : 42 is not a pointer
};
```

> Caution : Avoid Overuse of Conversion Functions

If there is no obvious mapping between Class to Type. eg. `Date` to `int`. 

If there is no clear one-to-one mapping. 

##### Conversion Operators Can Yield Surprising Results
It is not uncommon for classes to define conversion to `bool`. 

###### `explicit` Conversion Operators
```
class SmallInt{ 
public: 
	// the compiler won't automatically apply this conversion
	explicit operator int() const { return val; }
// other members as before
};
```

```
SmallInt si = 3; // Ok SmallInt constructor is not explicit
si + 3; // Error; implicit conversion required, 
// but operator int() is explicit

static_cast<int>(si) + 3; // ok explicitly request the conversion
```

We can still do the conversion, we just have to ask explicitly for it lol. 

This is through a cast: 
```
static_cast<int>(si); 
(int)si;
```

How do we explicitly cast: 
#explicitlycast
1. **Static Cast** `static_cast` :  can access user-defined conversions: `int x = static_cast<int>(3.14);`
2. **Dynamic Cast** `dynamic_cast` :  primary use for down casting in polymorphic class hierarchies, requires at least one polymorphic class (a class having at least one virtual function). Run-time type checking for safe conversion : `Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);`
3. **Const Cast** `const_cast` : removing `const, volatile`, might be useful for temp removing const : `const int* ptr = ...; int* nonConstPtr = const_cast<int*>(ptr);`. 

Try and use these more than anything like the C-style casting `(int)NotIntYet`. 
It lacks a lot of "safety", I'm not scared. 
They contain  **Limited Compile-Time Checking**. 

Explicit conversion will still be used implicitly to convert an expression used as: 
- The condition of an `if`, `while`, `do` statement
- The condition expression in a `for` statement header
- An operand to the logical `NOT(!), OR(||), AND(&&)` operators. 
- The condition expression in a conditional `(?:)` operator. 

###### Conversion to `bool`
The IO lib defines an `explicit` conversion to `bool`. 

When we use a stream object in a condition, we use the `operator bool`, that is defined for the IO types. 
```
while (std::cin >> value)
```
Here we are executing the input operator, which reads into `value` and returns `cin`. 
Then, to evaluate, `cin` is implicitly converted by the `istream operator bool` conversion function. 
Whatever that function does, it returns true if the condition state of `cin` is `good`, and false otherwise. 

> Conversion to `bool` is usually intended for use in conditions. As a result, `operator bool` ordinarily should be defined as `explicit`

