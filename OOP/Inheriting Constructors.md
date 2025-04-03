This reduces some of the boilerplate that we have to write. 

When we do something simple like this: 
```
struct B {
	B() : f(0.0f) {}
	B(double a) : f(a) {}


	double f;
};

struct D : public B { // remember that polymorphism only really works when we inherit publicly
};

int main() { 
	D d(1.0f);
	D e;
}
```

This will run an error as we have no Derived ctor that takes in a double, only the default one. 
`e` will be using the base default constructor for the variable `f`. 

However, let's put something into struct `D`
```
struct D : public B { 
	using B::B; // inheriting all the base constructors
	// Compiler will generate code such as
	// D(double f) : Base(f) {}
	// This will be generated for us
};
```


Therefore when we do something like this: 
`D d(1.0f);` 
This is defined and is fine for us to run. 

Therefore whenever you use inherited constructors, you have to think about what code is being generated. 

```
#include <iostream>
#include <string>

// Base class
class Base {
public:
    Base(int a) {
        std::cout << "Base constructor with int: " << a << std::endl;
    }
    
    Base(double b) {
        std::cout << "Base constructor with double: " << b << std::endl;
    }
    
    Base(const std::string& str) {
        std::cout << "Base constructor with string: " << str << std::endl;
    }
};

// Derived class
class Derived : public Base {
public:
    using Base::Base; // Inherit constructors from Base
};

int main() {
    Derived obj1(42);            // Calls Base(int)
    Derived obj2(3.14);          // Calls Base(double)
    Derived obj3("Hello World"); // Calls Base(const std::string&)
    
    return 0;
}

```

The compiler generated constructors will look something like this: 
```
class Derived : public Base {
public:
    // Inherited constructors
    Derived(int a) : Base(a) {}
    Derived(double b) : Base(b) {}
    Derived(const std::string& str) : Base(str) {}
};
```


And then we can add our own custom initialization. 

```
#include <iostream>
#include <string>

// Base class
class Base {
public:
    Base(int a) {
        std::cout << "Base constructor with int: " << a << std::endl;
    }
    
    Base(double b) {
        std::cout << "Base constructor with double: " << b << std::endl;
    }
    
    Base(const std::string& str) {
        std::cout << "Base constructor with string: " << str << std::endl;
    }
};

// Derived class
class Derived : public Base {
private:
    int derived_member;
public:
    using Base::Base; // Inherit constructors from Base

    // Custom constructor for additional initialization
    Derived(int a, int derived_val) : Base(a), derived_member(derived_val) {
        std::cout << "Derived constructor with int: " << derived_val << std::endl;
    }

    // Override the constructor that takes a double
    Derived(double b, int derived_val) : Base(b), derived_member(derived_val) {
        std::cout << "Derived constructor with double and int: " << derived_val << std::endl;
    }

    // You can still use inherited constructors for other types if needed
    Derived(const std::string& str, int derived_val) : Base(str), derived_member(derived_val) {
        std::cout << "Derived constructor with string and int: " << derived_val << std::endl;
    }
};

int main() {
    Derived obj1(42);                  // Calls inherited Base(int)
    Derived obj2(3.14);                // Calls inherited Base(double)
    Derived obj3("Hello World");       // Calls inherited Base(const std::string&)

    Derived obj4(42, 100);             // Calls Derived(int, int)
    Derived obj5(3.14, 200);           // Calls Derived(double, int)
    Derived obj6("Hello World", 300);  // Calls Derived(const std::string&, int)
    
    return 0;
}
```

There is also this cool use case, where we use `using` in order to change the accessibility of a member from the base class to the derived class: 
```
struct B {
	B() : f(0.0f) {}
	B(double a) : f(a) {}
	B(int a) : i(i) {}


protected:
	double f;
	int i;
};


struct D : public B { // remember that polymorphism only really works when we inherit publicly
public:
	using B::B;
	using B::f;

};



int main() // early on this is just acting as the placeholder for what is to come, i feel like minimal should be in here
{
	D d(1.0f);
	D e(1);

	std::cout << e.f << std::endl;
```

Notice that e, has public access to f, despite it being protected in the base class. 

