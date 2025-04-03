#classes 
#constructors

Each class defines how objects of its type can be initialized with constructors. 
Its job is to initialize the data members of a class object. 

They are more complex than what we make of them here, therefore they will be talked about later on as well. 

They have the same name as the class. 
Unlike other functions, they have no return type. 
They might have a parameter list, and a function bod, but that is not required of course. 

They may not be declared as const. 
The `constness` of an object is not assumed until after the constructor completes the object's initialization. Hence why we can write to `const` objects during their constructions. 

### Synthesized Default Constructor
The class we have written, the `Sales_data` does not have a defined constructor, yet the programs we've written that use `Sales_data` objects compile and run correctly. 

Classes control default initialization by defining a special constructor, known as the **default constructor**. 
This constructor takes no arguments. 

It is special in various ways, one being that if we do not explicitly define any constructors, the compiler will implicitly define the default constructor for us. 
This is the synthesized default constructor. 

Because `Sales_data` provides initializers for `units_sold` and `revenue`, the synthesized default constructor uses those values to initialize those members. 
It will default initialize the `bookNo` to the empty string. 

Some classes cannot rely on synthesized default constructors: 

And for some classes, the synthesized default constructor will do the wrong thing. 

Remember that built-in types will usually initialize to undefined, therefore we should in class initialize them at least. 

Using the default constructor: 
`MyClass obj;`



### Defining Constructors
For this class we will define four constructors with the following parameters: 
- An `istream&` from which to read a transaction
- A `const string&` representing `ISBN`, and `unsigned` representing the count of how many books were sold, and a `double` representing the price at which the books were sold. 
- A `const string&` representing an `ISBN`. This constructor will use default values for other members. 
- An empty paramete rlist (ie. the default constructor) which as we've just seen we must define because we have defined other constructors. 

Adding these members to our class, we now have: 
```
struct Sales_data{ 
	// constructors added
	Sales_data() = default; 
	Sales_data(const std::string &s) : bookNo(s) {}
	Sales_data(const std::string &s, unsigned n, double p) : bookNo(s), units_sold(n), revenue(p*n){}
	Sales_data(std::istream &);


	// other members
	std::string isbn() const( return bookNo;)
	Sales_data& combine(const Sales_data&);
	// etc.
	std::string bookNo;
	unsigned units_sold = 0;
	double revenue = 0.0;
}
```
Other members are all the same. 

### What = `default` means
`Sales_data() = default;`
This marks the default constructor because it takes no arguments. 
We are defining this *only* because we want to provide other constructors as well as the default constructors. 
We want this constructor to do exactly the same work as the synthesize version we had been using. 

The new standard is actually using the `= default` after the parameter list. 

### Constructor Initializer List
The other constructors that we used, such as these two: 
```
Sales_data(const std::string &s) : bookNo(s){}
Sales_data(const std::string &s, unsigned n, double p) : bookNo(s), units_sold(n), revenue(p*n) {} 
```
The new parts in these are the colon and the code between it and the curly braces that define the (empty) function bodies: 
You can also use curly braces instead of brackets. 

When a member is omitted from the constructor initializer list, it is implicitly initialized using the same process as is used by the synthesized default constructor. Thus, that constructor that takes only a `strong` is equiv to: 
`Sales_data(const std::string &s) : bookNo(s), units_sold(0), revenue(0) {}`

It is usually best for a constructor to use an in-class initializer if one exists and gives the member the correct value. 
If in-class initializers are not supported, then we should explicitly initialize every member of built-in type. 

Both have empty function bodies. 
The only work these constructors need to is give the data members their values. If nothing else is required, then we can obviously leave them empty. 

Unlike these constructors, the `istream` constructor does have work todo. This constructor calls read to give the data members new values: 
```
Sales_data::Sales_data(std::istream &is){
	read(is, *this); // read will read a transaction from is into this object. 
}
```

REMEMBER that we have o define which class this constructor will come from, which looks silly as you just double the name around `::`. 
It's more correct to say that the constructor initializer list is empty (although it looks non-existent), even though the list is empty, the members of this object are still initialized before the constructor body is executed. 
They will be initialized by the in-class initializer (if there is one) or are default initialized. 
Meaning that here, the `bookNo` will be empty `string`, `units_sold` and `revenue` will be 0. 
Remember that `*this` will access the object as a whole, not a member. 
Passing `this` will be a pointer to an object, and you could use that if you dereference I guess, however, passing the object as a whole makes more sense here. 

#### From ChatGPT
```
class Person{ 
	private: 
		std::string name;
		int age;
		
	public:
		// default constructor
		Person() { 
			name = "Unknown";
			age = 0;
		}

		// Constructor with parameters
		Person(std::string n, int a){ 
			name = n;
			age = a;
		}


};
```


It also says about `synthesised default constructor` is the idea of the compiler creating a default constructor for you, this is strictly default. Implicitly created, if explicitly not defined. 

```
class Example{ 
	private: 
		std:;string name;
		int age; 

	public: 
		viod display(){ 
			std::cout << "Name: " << name << ", Age: " << age << std::endl;}
};
```

These are only provided if there are no constructors at all: 


```
class Example{

public:
    int x;
    double y;

    int z = 42;
    double w = 3.14;
	
    Example(int a, double b) : x(a), y(b) { cout << "CONSTRUCT 1" << endl;}

    Example(int a) : x(a), y(0.0) {cout << "CONSTRUCT 2" << endl;}
};

int main(){
    Example obj1(10,2.5);
    cout << obj1.z << "   " << obj1.w << endl;

    Example obj2(5);
    cout << obj2.z << "   " << obj2.w << endl;
```

![[Pasted image 20240301010707.png]]