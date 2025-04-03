*The* idea is super simple, saving code and time and lines of code that we don't need to write. 
Within the initializer, we call another constructor, remember that they are essentially just functions. 

Here are the basics: I would copy this into vsCode just to see the outputs too. 
A big thing to remember is that the delegated to constructor (function) will complete fully, so the body is fulfilled before returning back to the other (delegated from) constructor finishes its body. 

Here is a basic one: 
```
class Vehicle{ 
	std::string type; 
	int year;

public: 
	// Default Constructor, as in now agruments given in object creation
	Vehicle () : Vehicle("Unknown", 0) {cout << "Default constructor called:" << endl;}
	// This will only be called when creating an object with no arguments


	// Base constructor with type and year parameters
	Vehicle(const std::string& t, int y) : type(t), year(y) {cout << "Base Constructor called, initializing both type and year, delegating to:   ";}


	// Only type arguments, no year
	Vehicle(const std::string t) : Vehicle(t, 0) { cout << "Construct with only type arguments, delegating to:   ";}


	// Only year arguments, no type
	Vehicle(int y) : Vehicle("Unkown", y) { cout << "Construct with only year called, delegating to:   ";}

};
```


Function matching is a weirdly big thing here, in that we find the constructor that best fits the arguments we give. If that delegates to another, then that's fine too. 

We usually delegate to a constructor that will initialize all of the members. 
Something to note and remember here is that the base constructor body will be executed first, as we called that function til completion first. 

A DELEGATING CONSTRUCTOR CANNOT ALSO HAVE MEMBER INITIALIZATIONS

So you couldn't do something like this: You can have member initializers, or delegating constructors, NOT BOTH. 
`class_a(string str, double dbl) : class_a(str), m_double{dbl} {}`
This won't work, as the compiler will say that the delegating constructor, (the first part of this constructor denotes), cannot also have a member initializer. 

There is also an issue with this: 
`class_a(string str, double dbl) : class_a(str) { m_double = dbl; }`
I can't understand the alien language that it gives as the error here:


```
class class_a{ 
public: 
	class_a() {}
	class_a(string str) : m_string{ str } {}
	class_a(string str, double dbl) : class_a(str)
	
	double m_double{ 1.0 };
	string m_string{ m_double < 10.0 ? "alpha" : "beta" };
};
```

This is next one is weirdly fine: 

```
class class_a {

public:

    class_a() {cout << "Default called" << endl;}

    class_a(string str) : m_string{ str } {cout << "m_string initializer" << endl;}

    class_a(string str, double dbl) : class_a(str) { m_double = dbl; cout << "delegate to string, double initializer;" << endl; }

  

    double m_double{1.0};

    string m_string { m_double < 10 ? "alpha" : "beta"};

};

  

int main(){

    class_a a{"hello", 2.0};

    cout << endl;

    class_a b;

    cout << endl;

    class_a c{"", 15};

    cout << a.m_double << " " << a.m_string << endl;

    cout << b.m_double << " " << b.m_string << endl;

    cout << c.m_double << " " << c.m_string << endl
```
You can do the whole: 
`class_a(string str, double dbl) : class_a(str) { m_double = dbl;}`
Is also fine.


The website also says to avoid cycles too: 
Such as: 
```
class class_f{ 
public: 
	int max; 
	int min; 

	// don't do this
	class_f() : class_f(6,3){}
	class_f(int my_max, int my_min) : class_f() {}
};
```

You can clearly see here that we would be filling the function stack calls. 



