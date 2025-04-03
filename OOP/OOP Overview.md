Data abstraction - levels of encapsulation: 

We'll take a loo at Inheritance, Dynamic Binding; how they make our programs easier, defining similar classes. 

Data abstraction, inheritance, dynamic binding. 

### Inheritance
Inheritance related classes form a hierarchy. The base classes, at the root, from which other classes inherit, directly or indirectly. 
These inheriting classes are known as derived classes. 

Base class - common to all the derive. Each derived, defining members that are specific - shapes -> circles, triangles, squares. 

#virtual
The base class will define `virtual` to those functions that it expects its derived classes to define for themselves. 

So we might have a class that looks like this: 
```
class Quote{ 
public: 
	std::string isbn() const;
	virtual double net_price(std::size_t n) const;
};
```

A derived class must specify the class(es) from which it intends to inherit. 

Using the **class derivation list**, colon followed by comma separated list from all that we are inheriting from, then there is the optional specifier as well: 
```
class Bulk_quotes : public Quote { // Bulk_quote inherits from Quote

public: 
	double net_price(std::size_t) const override;
};
```
As our `Bulk_quotes` are using `public` in the derivation list : we can use objects of type `Bulk_quote` as if they were `Quote` objects.

We have to declare all the functions that were declared `virtual` in the base class. 
The derived class may include the `virtual` keyword on these functions but it's not required to do so. 

#override
We can also `override` a virtual that it inherits too, it's better to use `override`. 

### Dynamic Binding
Here, we can use the same code to process objects of either type `Quote` or `Bulk_quote` interchangeably. 
The following function prints the total price for purchasing the given number of copies of a given book: 
```
// calculate and print the price for the given number of copies, applying any discounts
double print_total(ostream &os, 
	const Quote &item, size_t n)
{ 
	// depending on the type of the object bound to the item parameter
	// calls either Quote::netprice or Bulk_quote::net_price
	double ret = item.net_price(n);
	os << "ISBN: " << item.isbn() // calls Quote::isbn
	<< " #sold " << n << " total due: " << ret << endl;
	return ret;
}
```

The `item` parameter is a reference to `Quote`, we can call this function on either a `Quote` or a `Bulk_quote` object. As `net_price` is a virtual function, and because we are calling `print_total` from a reference, the version run, will depend on the type of the object that we pass to `print_total`. 

```
// basic has type Quote; bulk has type Bulk_quote
print_total(cout, basic, 20); // calls the Quote version of net_price

print_total(cout, bulk, 20); // calls Bulk_quote version of net_price
```

The first passes a `Quote` object to `print_total`. 
The next passes a `Bulk_quote`, so the `Bulk_quote` version of `net_price` (which applies a discount). 

The decision of which to run depends on the argument that we pass into it. 

This decision can't be made until run time. 
Therefore, dynamic binding is sometimes called as **run-time binding**. 

> In C++ dynamic binding happens when a virtual function is called through a reference (or a pointer) to a base class. 

#definingbaseclass
#definingderivedclass

### Defining Base and Derived Class
###### Defining a Base Class

We'll start by completing the definition of our `Quote` class: 
```
class Quote{ 
public: 
	Quote() = default; // 
	Quote(const std::string & book, double sales_price) : 
		bookNo(book), price(sales_price) {}
	std::string isbn() const { return bookNo; }

	// returns the sales price for the specified number of items
	// derived classes will override and apply different discount algorithms 

	virtual double net_price(std::size_t n) const 
		{return n*price;}

	virtual ~Quote() = default;
	// dynamically binding destructor

private: 
	std::string bookNo; // ISBN number fo this item
	
protected: 
	double price = 0.0; // normal, undiscounted price
};
```

The new bits are `virtual` on `net_price` function and the destructor. 
Also the `protected` access specifier. 

Classes that are used as the root of an inheritance hierarchy almost always define a virtual destructor. 

> Base classes should define a virtual constructor (we'll see exactly why). Virtual destructors are needed even if they do no work.


##### Member Functions and Inheritance
Derived classes inherit the members of their base class. 

A derived class needs to be able to provide is own definitions for operations, such as `net_price`, that are type dependent. 
In this case, the derived class needs to **override** the definition it inherits from the base class, by providing its own definition. 

**In our base class, we have to be explicit over what functions we want our derived classes to override, or those that we want the derived classes to inherit with no change**. 

`virtual` for those we want our derived classes to override. 

When we call with a reference and a pointer, the call will be dynamically bound. 

Depending on the type of the object to which the ref or pointer is bound, the version in the base, or derived will be called. 

Any non`static` function, other than constructors (destructors are fine however), can be virtual. 

Only on declaration inside the class, cannot appear on the definition outside the class (in-class specific here). 

A function that is declared as `virtual` in the base class is implicitly `virtual` in the derived class as well. 

Those that are not declared as `virtual` are resolved at compile time. 

If a function will run consistently throughout the hierarchy, then we don't need the virtual. 

##### Access Control and Inheritance
Derived Class: inheriting all that from base class. 
A derived class may access the `public` members of its base class but may not access the `private` members. 
Sometimes we want the base class to have members that our derived classes wil use, while preventing other users from using the same members. 
We specify these members after a `protected` access specifier: meaning that they are protected by class (lol) and the outside world can't get them, but the "inheritance is protected". 

So the `Quote` class here, expects its derived classes to define their own. 
To do so, they need access to the `price` member, therefore, that member needs to be `protected` **not** `private`. 
If it were `private` then we would run into accessing errors. 

### Defining a Derived Class
#definingderivedclass

**Class Derivation List**, showing all the classes that we are inheriting from. 

Each base class that we inherit from, may have an access specifier, which is one of `public, protected, private`. 

Here: 
```
class Bulk_quote : public Quote { 
// inheriting from Quote
	Bulk_quote() = default;
	Bulk_quote(const string&, double, std::Size_t, double);

	// overrides the base version in order to implement the bulk purchase discount policy
	double net_price(std::size_t) const override; 

private:  // only for this class 
	std::size_t min_qty = 0; // minimum purchase for the discount to apply
	double discount = 0.0; // franctional discount to apply
};
```

Here we are inheriting the `isbn()` function and the `bookNo` and `price` data members of its `Quote` base class. 
Defining its own version of `net_price` and has two additional data members, `min_qty` and `discount`. 

When derivation of `public`, the `public` members of the base class become part of the interface of the derived class as well. **Additionally**, we can bind an object of a publicly derived type to a pointer or reference to the base type: 
`BaseClass * DerivedObject = DerivedClass(//args);`
Due to this `public` we can use a `Bulk_quote` object where a pointer or reference to `Quote` is expected.

**Single Inheritance**, just inheriting from one class (usually better than **Multiple Inheritance**, especially in game engines). 

You want to avoid the dreaded diamond. 

#### Virtual Functions in the Derived Class
Derived classes, sometimes, a lot, override the virtual functions that they inherit. 
If a derived class does not override a virtual from its base, then, like any order member, the derived class will inherit the one that is defined in its base class. 


A derived class may also use the `virtual` on the functions it overrides, however, it doesn't require it to do so. 

We can also specify the `override` after the parameter list, or after the `const` or reference qualifier(s) if the member is a `const` or reference function. 

####### Derived-Class Objects and the Derived-to-Base Conversion
A derived object has many a part to it; a subject containing the (non`static`) members defined in the derived class itself, plus subobjects corresponding to each base case from which the derived class inherits. 
So a `Bulk_quote` object will contain four data elements: 
`bookNo` and `price` data members inherits from `Quote`, and the `min_qty` and `discount` members which are defined by `Bulk_quote`. 

Although the standard does not specify how derived objects are laid out in memory. ![[Pasted image 20240503152721.png]]

![[Pasted image 20240503152750.png]]

> **The base and derived parts of an object are not guaranteed to be stored contiguously. The diagram above is just a concept, not physical, representing how classes work.**

As these objects contain some parts of the base, we can essentially use the derived object *as if* it were an object of its base type(s). 
We can bind a base-class reference or pointer to the base-class part of the derived object. 

```
Quote item; // base 
Bulk_quote bulk; // derived object
Quote *p = &item; // p points to a Quote object
p = &bulk; // p points to the Quote park of bulk 
Quote &r = bulk; // R bound to the Quote part of bulk
```

This is often called a **Derived-to-Base** conversion. 
This can be done implicitly as well. The compiler will do a lot of work for us. 

Meaning that we can use a pointer or reference to a derived type when a reference to the base type is required. 

So the derived object, has, for the most part, the Base class in it, so we can just use the derived object when we should be using the Base class. 

##### Derived-Class Constructors 
Although a derived object contains members that it inherits from its base class, it cannot initialize them directly.
A derived class, has to use a base-class constructor in order to initialize its base-class part. 
We have to use the base-class constructor to init those members of base class. 
> **Each class controls how its members are initialized** 

The base part of the class is initialized, along with the members of the derived class, during the initialization phase of the constructor. 

```
Bulk_quote(const std::string& book, double p,
	std::size_t) : 
	Quote(book, p), min_qty(qty), discount(disc){}
// other members as before
```

Here we are passing the first two parameters  to the `Quote` constructor. 
There, the `Quote` constructor will initialize the obj's base class part (the class's base class part). 
After that ctor function body is complete, then we move to the derived class's ctor body. 

Unless we say otherwise, the base part of the derived object is default initialized (default ctor `Base()`). 
Or we just use the base-class ctor, by providing the ctor initializer using the name of the base class, then the parameters. 

It's not making it's own base class object, it's included in the object that we are defining. 
>Remember that the base class is initialized first. And then the derived class members are initialized in the order in which they are declared in the class. 

##### Using Members of the Base Class from the Derived Class
A derived class, access to `public, protected` members of its base class: 
```
double Bulk_quote::net_price(size_t cnt) const 
{ 
	if(cnt >= min_qty)
		return cnt*(1 - discount) * price;
	else 
		return cnt*price; 
}
```

The scope of a derived class, is nested inside the scope of its base class. 
There is, therefore, no distinction between how a member of the derived class uses members defined in its own class and how it uses members defined in its base. 

> **Respecting the Base-Class Interface**
> Each class defines its own interface. Interactions with an object of that class type, will utilise the interface of that class, even if that object is the base-class part of the derived object. 
> As a result, derived-class ctors may not directly initialize the members of its base class. 
> The ctor body of the derived ctor can assign values to its `public` or `protected` base class members. Although it can *assign* here, it generally should not do so. 
> WE HAVE TO RESPECT THE INTERFACE of the BASE CLASS, BY USING THE CTOR to INITIALIZE its INHERITED MEMBERS.


###### Inheritance and `static` Members
If the base class has a `static` member, there is only one such member defined for the entire hierarchy. There is just the one, despite how many inheriting classes there are, there is still only one single instance of each `static` member, there's only one, only one. 

```
class Base{ 
public: 
	static void statmem();
};

class Derived : public Base{ 
	void f(const Derived&);
};
```
`static` members obey normal access control. `private` would mean inheriting classes have no access to it. 
If accessible, we can use the member through either the base or derived: 
```
void Derived::f(const Derived & derived_obj)
{ 
	Base::statmem(); // ok: Base Defines statmem
	// remember that statmem should be defined outside the Base class
	Derived::statmem(); // Ok derived inherits statmem
	derived_obj.statmem(); // accessed through a Derived Object
	statmem();
}
```


```
class Base {
public:     
    int value; 
    string name; 
    static size_t counter;
    
    static void BCounter();

    Base() : Base(0, "Default") {}

    Base(int a, string n) : value(a), name(n) {}

    void BPrintOut() {
        cout << value << "   " << name << endl;
    }
};


class Derived : public Base {
public:
    string d_name; 

    Derived() = default;
    Derived(string n) : d_name(n) {}

    Derived(int a, string n, string dn) : Base(a, n), d_name(dn) {}


    void DPrintOut() {
        cout << d_name << endl;
    }


    void FullPrint() {
        cout << value << "  " << name << "    " << d_name;
    }
};

size_t Base::counter = 0;

void Base::BCounter(){
    cout << "Called BCounter" << endl;
    ++counter;
    cout << "Counter at: " << counter << endl;
}

int main(){
    Derived dObj("Hugo");
    dObj.BCounter();
    dObj.BCounter();
    dObj.BCounter();
}
```

This is a good example for me. 

#### Declarations of Derived Class
You do not need to include its derivation list: 
```
class Bulk_quote : public Quote; // WRONG
class Bulk_quote; // Ok right way to declare
// don't need to declare a derived class
```

##### Classes Used as a Base Class
A class has to be defined, not just declared, before we can use it as a base class: 

```
class Qutoe; // declared not defined
// error: Quote must be defined
class Bulk_quote : public Quote {...};
```

Each derived class contains, and may use, the members it inherits from its base. To use those members, we have to know what they are, not just declared. 
One implication of this, is that we cannot derive a class from itself: 
```
class Base { /* ... */ };
class D1 : public Base { /* ... */ };
class D2 : public D1 { /* ... */ };
```

`Base` is a direct base to `D1` and an indirect base to `D2`. 
Each class inherits all the members from its direct base class. 
The members in the direct base include those it inherits from its base class, all the way through the inheritance chain. 

Effectively, the most derived object contains a sub-object for its direct base and for each of its indirect bases. 

##### Preventing Inheritance
Sometimes we define a class that we don't want to inherit from others. 
We can prevent a class from being a base class by using `final`: 
```
class NoDerived final { /* ... */ }; // Can't be a base class
class Base { /* ... */ };
// Last is final, cannot inherit from Last
class Last final : Base {/* ... */ }; 
class Bad : NoDerived { /*...*/ }; //error : NoDerived is final
class Bad2 : Last { /* */ }; // Error Last is final!
```


##### Conversions and Inheritance
Understanding conversions between base and derived classes is essential to understanding how object-orientated programming words in C++. 

Normally, we bind a pointer or a reference to the type of the object, or a type that involves an acceptable `const` conversion. 

Classes that are related by inheritance are an important exception: 
We can bind a pointer or reference to a base class type to an object of a type derived from that base class. 

We can use a `Quote&` to refer to a `Bulk_quote` object, and we can assign the address of a `Bulk_quote` object to a `Quote*`. 

This has a massive implication: 
When we use a reference (or pointer) to a base-class type, we don't know the actual type of the object to which the pointer or reference is bound. 
That object can be an object of the base class or it can be an object of the derived class. 

> Like built-in pointers, the smart pointer classes support the derived-to-base conversion - we can store a pointer to a derived object in a smart pointer to the base type. 

##### Static and Dynamic type 
Typing related to inheritance, we often need to distinguish between the **static** and **dynamic** type of the object that expression represents. 
The static type is always known at compile time - it is the type with which a variable is declared or than an expression yields. 
The dynamic type is the type of the object in memory, that the variable or expression represents. 
The dynamic type might not be known until runt ime: 
eg. `print_total` calls `net_price`
`double ret = item.net_price(n);`
We know that the static type of `item` is `Quote&`. The dynamic type is the type of the argument to which `item` is bound. 

Another example would be : 
```
int x = 5; // static type of x is `int`
Animal* ptr = new Dog(); // Static type of ptr is Animal*
// even though it is pointing to a Dog object
```
Determined by the declaration of the variable or the type of the object being created. 
The static type is used by the compiler for type checking, function overloading resolution, and determining the layout of data structures. 

Therefore the dynamic type of an object is the actual type of the object it refers to at runtime. It's determined by the other side of the expression. 
The dynamic type is used at runtime for dynamic dispatch (polymorphism), determining which function to call when using virtual functions. 
```
Animal* ptr = new Dog(); // The dynamic type here is Dog
```

This dynamic type cannot be known until run time. 

The dynamic type of an expression that is neither a reference nor a pointer is always the same as that expression's static type. 
A variable of type `Quote` is always a `Quote` object; there is nothing we can do that will change the type of the object to which that variable corresponds. 

 > Just remember that the static type of a pointer or reference to a base class may differ from its dynamic type. 



#### There is no Implicit Conversion from Base to Derived... 
The conversion from derived to base exists, as every derived object contains a base-part to which a pointer or reference of the base-class type can be found. 

There is no similar guarantee for base-class objects. 

A base class object can exist either as an independent object or as part of a derived object. 

A base object, that is not part of a derived object has only the members defined by the base class; it doesn't have the members defined by the derived class. 

Because a base object might or might not be part of a derived object, there is no automatic conversion from the base class to its derived class: 
The Ideology is that there is the base class part of derived objects already, however, the base class object does not have any of the derived classes parts, which makes a lot of sense, therefore, there is no implicit conversion. 

```
Quote base; 
Bulk_quote* bulkP = &base; // Error: can't convert base to derived

Bulk_quote& bulkRef = base; // Eror can't convert base to derived
```

If these assignments were legal , we might attempt to a use `bulkP` or `bulkRef` to use members that do not exist in `base`. 

What might be a bit surprising is that we can't convert from base to derived even when a base pointer or reference is bound to a derived object: 

```
Bulk_quote bulk;
Quote* itemP = &bulk; // Dynamic type is Bulk_quote


Bulk_quote* bulkP = itemP; // Error
// can't convert base to derived
```

So we can convert from derived to base, as the base part of the class is still there in the object. However, base to derived is harder, as there are no derived parts to the base object's class type. 

At compile time, the compiler will have no ways of knowing if the conversion will be safe. 
The compiler will only look at the static types of the pointer or reference to determine whether a conversion is legal. 

> The compiler will only look at the static type to know whether a conversion is legal. 

If the base class has one or more virtual functions, we can use a `dynamic_cast` to request a conversion that is checked at run time. 

In cases where we know that going from base to derived is actually safe, we use the `static_cas` to override the compiler here. 


##### ... and No Conversion Between Objects
The automatic derived-to-base conversion applies only for conversions to a reference or pointer type. 

There is no such conversion from a derived-class type to the base-class type. 

It is often possible to convert an object of a derived class to its base-class type. 

These conversions here might not behave as we want. 

Remember that when we initialize or assign an object of a class type, we are actually calling a function. 

Initialize - we're calling a constructor. 
When we assign - calling an assignment operator. 
These, normally have a parameter that is a reference to the `const` version of the class type. 

As these members take references, the derived-to-base conversion lets us pass a derived object to a base-class copy/move operation. 

These operations are not virtual. 
When we pass a derived object to a base class constructor, the constructor that is run is defined in the base class. 
That constructor knows only about the members of the base class itself. 

 Similarly if we assign a derived object to a base object, the assignment operator that is run is the one defined in the base class. 
That operator also knows only about the members of the base class itself. 

As an example, our bookstore classes use the synthesized version of copy and assignment. 
For now, synthesized versions memberwise copy or assign the data members of the class the same way as for any other class: 
```
Bulk_quote bulk; // object of derived type
Quote item(bulk); // uses the Quote::Quote(const Quote&) ctor

item = bulk; // class Quote::operator =(const Quote&)
```

When `item` here is constructed, the `Quote` copy constructor is run. 
That constructor only knows about the `bookNo` and `price` members. 
It will copy those members from `Quote`

[Virtual Functions]



#memberwisecopy
This is referring to the copying of individual data members (or fields) of one object to another object of the same type. 

Using the copy ctor or the assignment operator. 
```
class MyClas{ 
public: 
	int x; 
	double y; 
	
	MyClass(int x_val, double y_val) : x(x_val), y(y_val) {}

	void display() { 
		cout << x << y << endl;
	}
};

int main(){ 
	MyClass obj1(5, 3.14);
	MyClass obj2 = obj1; // Memberwise copy performed here
	// This is a synthesized function as we have not defined it
}
```
These objects now have the same member values. 

