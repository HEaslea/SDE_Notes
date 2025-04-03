#classes #definingabstractdatatypes
#constructors 
#constructorsrevisited

They are a crucial part of the whole class thing that we have going on here. 

We've covered the basics so far :)

### The constructor initializer List
When defining variables, we typically initialize them immediately rather than defining them and then assigning them: 
```
string foo = "Hello World"; // define and initialize
string bar; // default initialized to the empty string
bar = "Hello World"; // assign a new value to bar
```

These ideas apply to data members as well, if we don't explicitly initialize a member in the constructor init list, that member is default init before the constructor body starts executing: 

eg: 
```
// legal but sloppier way to write the Sales_data constructor: no constructor initializers
Sales_data::Sales_data(const string &s, unsigned cnt, double price)
{ 
	bookNo = s;
	units_sold = cnt;
	revenue = cnt * price;
}
```
When the constructor finishes, the data members will hold the same values. 

The difference is that the original version we made initializes its data members, whereas this assigns values to the data members. 

##### Initializers are Sometimes Required
Often, it's fine to ignore the distinction made above, however, as usually `const` and `references` must be initialized. 
Members that are of a class type that does not define a default constructor also must be initialized: 
```
class ConstRef{ 
public: 
	ConstRef(int ii);
private: 
	int i; 
	const int ci; 
	int &ri;
};
```
Here we would get an error as we have no initializer for the const and reference type. 
Like in main, you can't just write: 
`const int ci;` as the compiler/intelligentsense will ask you for a initializer. 

```
// error: ci and ri must be initialized
ConstRef::ConstRef(int ii){ 
	i = ii; // ok
	ci = ii; // error: cannot assign to a const
	ri = i; // error : ri was never initialized
}
```

`ci` and `ri` cannot be assigned values directly within the constructor body: this is because ci cannot be assigned after it is initialized, and reference members must be bound to a valid object upon construction. By the time the constructor begins executing, initialization is complete. 
Our only chance to initialize here is in the **constructor** initializer. 
Here: 
```
// ok: explicitly initialize reference and const members
ConstRef::ConstRef(int ii) : i(ii), ci(ii), ri(i) {}
```

This is the case to remember: By the time the constructor body is hit, initialization is already done. 

We **have to** use the initializer list to provide values for members that are `const` and `reference`, and also for a `class type` that does not have a default constructor. 


Why would we want to initialize and then assign, when we can just do both in the initializer list. 
Moreover, we avoid the fact that we have to think a lot about members that need to be initialized. 
You can avoid the compile time errors. 

#### Order of Member Initialization
We only wanna name each member once, don't wanna double initialize. 
They are init'd in the order that they appear; 

This doesn't often matter, if one member is initialized before another, then the order in which members are inti'd is crucial. 
```
Class X{ 
	int i;
	int j;
public:
	// undefined: i is initialized before j
	X(int val); j(val), i(j) {}
};
```

Here, the constructor inti list makes it *appear* as if j is init'd with val, and then j is used to init i, however, i is initialized first. 
The effect of this initializer is to initialize i with the undefined value of j. 

Some compilers are kind enough to generate a warning if the data members are listed in the constructor initializer in a different order from the order in which they are declared. 

Therefore you should probably just write them both in the same order. Also avoid using members to initialize other members. 

It would just be better here to write what we have above as: 
`X(int val) : i(val), j(val) {}`


#### Default Arguments and Constructors
```
class Sales_data{ 
public:
	// defines the default constructor as well as one that takes a string argument
	Sales_data(std::string s = "") : bookNo(s) {}
	// remaining constructors are unchanged
	Sales_data(std::string s, unsigned cnt, double rev):
		bookNo(s), units_sold(cnt), revenue(rev*cnt) {}
	Sales_data(std::istream &is) { read(is, *this);}
	// remaining members as before
};
```

A constructor that supplies default arguments for all its parameters also defines the default constructor: 

```
struct X {
    X(int i, int j) : base(i), rem(base % j) {}
    int rem, base;
};
```
Remember that class members are initialized in the order they are declared, not in the order they appear in the list. 
It's as though the class takes note of the declared, and when it has a change to initialize them, they are in memory, in the order they are declared, therefore they get some values applied to them. So here rem is initialized first, however, it relies on base, therefore we are going to run into error: 
I would like to rewrite it like this: 
```
struct X { 
	int base, rem;
	X(int i, int j) : base(i), rem(base % j){}
};
```
My compiler (clang / g++) doesn't really have an issue with that; however, you don't wanna wait til compiler error for this to become part of your coding. 

### Delegating Constructors
#delegatingconstructors

A delegating constructor uses another constructor from its own class to perform part or all of its initialization. 
"Delegate" all or some of its work to this other constructor. 
It, too will have an init list and a body, like any other constructor. 
The member initializer list here has a single entry that is the name of the class itself. 
The name of the class is followed by a parenthesized list of arguments. 
The argument list must match another constructor in the class. 
Here is an example: 

```
class Sales_data{ 
public: 
	// non delegating constructor initializes members from corresponding arguments
	 Sales_data(std::string s, unsigned cnt, double price) : bookNo(s) , units_sold(cnt), revenue(cnt*price) {}
	 
	// Remaining constructors all delegate to another constructor
	// all of these are delegators
	Sales_data():Sales_data("", 0,0) {}
	Sales_data(std::string s):Sales_data(s,0,0){}
	Sales_data(std::istream &is):Sales_data() { read(is, *this);}
	//other members as before
};
```
In this version, all but one of the constructors delegate their work. 
The first takes three arguments, uses those arguments to initialize the data members, and does no further work. 
We define the default constructor to use the three-argument constructor to do its initialization. 
It too has no additional work, as indicated the empty constructor body. 
The constructor that takes a string also delegates to the three-argument version. 

The one that takes an `istream&` also delegates. It delegates to the default, which also delegates to the three-argument constructor. Once they complete their work, the body of `istream&` constructor is run. 
That calls `read` to read the given `istream`. 

When a constructor delegates to another constructor, the constructor initializer list and function body of the delegated-to constructor are both executed. 

The function bodies of the delegated-to constructors happen here to be empty. Had the function bodies contained code, that code would be run before control returned to the function body of the delegating constructor. It's like calling that function before running your own. 

So you move over to the delegated to, all of that runs, then we move back, like a function in a function. 

It's like having multiple entry points to initialize an object. 

Multiple constructors, each taking different parameters, instead of duplicating initialization code in each, you can have one constructor delegate the init to another one. 

Remember that the default constructor will be the one that doesn't take any arguments. 

Mainly they are for reducing repetitive parts of code, just shirk off the initializing to another. 

[[Delegating Constructors]]

[[The Role Of the Default Constructor]]