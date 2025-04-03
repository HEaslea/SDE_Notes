Like any other class, a class in an inheritance hierarchy controls what
happens when objects of its type are created, copied, moved, assigned, or
destroyed. As for any other class, if a class (base or derived) does not itself
define one of the copy-control operations, the compiler will synthesize
that operation. Also, as usual, the synthesized version of any of these
members might be a deleted function.

#### Virtual Destructors
The base class should general define a virtual destructor. 
This needs to happen to allow objects in the inheritance hierarchy to be dynamically allocated. 
The destructor is run when we `delete` a pointer to a dynamically allocated object. 

If the pointer points to a type in an inheritance hierarchy, it is possible that the static type of the pointer might differ from the dynamic type of the object being deleted. 
If we `delete` a pointer of type `Quote*`, that pointer might point at a `Bulk_quote` object. 
If the pointer does point to the `Bulk_quote` object, then the compiler will have to know to run the `Bulk_quote` destructor, not the static type destructor. 
```
class Quote{
public: 
	// virtual destructor needed if a base pointer pointing to a derived object is deleted
	virtual ~Quote() = default;// dynamic binding for the destructor
};
```
So long as this destructor in the base class is virtual, then we can even used the synthesized version of the derived overridden destructor. 

```
Quote* itemP = new Quote; // same static and dynamic type
delete itemP; // Quote destructor
itemP = new Bulk_quote; // static and dynamic types differ
delete itemP; // destructor for Bulk_quote called
```

God knows what would happen if the static type destructor was used on a derived object. 

##### Virtual Destructors Turn Off Synthesized Move
If a class defines a destructor - even if it uses `= default` to use the synthesized version - the compiler will not synthesize a move operation for that class. 

#### Synthesized Copy Control and Inheritance
The synthesized copy-control members in a base or a derived class
execute like any other synthesized constructor, assignment operator, or
destructor: They memberwise initialize, assign, or destroy the members of
the class itself. In addition, these synthesized members initialize, assign,
or destroy the direct base part of an object by using the corresponding
operation from the base class. 
- The synthesized `Bulk_quote` default constructor runs the `Disc_quote` constructor, which in turn runs the `Quote` default constructor. 
- The `Quote` constructor inits the `bookNo` member to the empty string and uses the in-class initializer to initialize `price` to zero. 
- When the `Quote` constructor is finished, the `Disc_quote` constructor continues, which will in-class initialize `qty` and `discount`. 
- When the `Disc_quote` ctor finishes, the `Bulk_quote` ctor continues but has no other work to do. 
So we essentially move to the base, and then down the hierarchy. 
All that we really have to think about here is whether the base class (or direct base class, one above the derived class), has that member accessible. 
We just have to make sure that that member function is not deleted. 

Each of our `Quote` classes can use synthesized destructors. 
The derived classes do so implicitly (by not writing anything at all), the base class `Quote` will have to do so explicitly by defining its (virtual) destructor as `= default`. 
The destructors will move up the chain, starting at the derived object, and then they move up and up, til the base object is destroyed. 
`Quote` apparently doesn't have synthesized move operations as it defines a destructor. 
The (synthesized) copy operations will be used whenever we move a `Quote` object. 
`Quote` not having a move operation, means that its derived classes don't either.
#### Base Classes and Deleted Copy Control in the Derived
The synthesized default constructor, or any of the copy-control members of either a base or a derived class, may be defined as deleted for the same reasons as we might have in any other class. 
The way in which a base class is defined can cause a derived-class member to be defined as deleted: 
- If the default constructor, copy constructor, copy-assignment operator, or destructor in the base class is deleted or inaccessible, then the corresponding member in the derived class is defined as deleted, as the compiler can't use the base-class member to construct, assign, or destroy the base-class part of the object. 
- If the base class has an inaccessible (via access modifiers) or deleted destructor, then the synthesized default and copy constructors in the derived classes are defined as deleted too, there is no way to destroy the base part of the derived object. 
- The compiler will not synthesize a deleted move operation. If we use `= default` to request a move operation, it will be deleted function in the derived if the corresponding operation in the base is deleted or inaccessible, as the base class part cannot be moved. Usually stuff in the base not being able to happen, means that it won't be able to happen in the derived. The move constructor will also be deleted if the base class destructor is deleted or inaccessible. 

Take this example: 

```
class B{ 
public: 
	B(); 
	B(const B&) = delete;
	// other members, not including a move constructor!
};

class D : public B{ 
	// no constructors
};

D d; // ok D's synthesized default constructor uses B's default constructor

D d2(d); // error D's synthesized copy constructor is deleted 
// due to B's being deleted. 

D d3(std::move(d)); // error; implicitly uses D's deleted
// copy constructor
```
Class `B` here has an accessible default constructor and an explicitly deleted copy constructor. 
As the the copy constructor is defined, the compiler will not synthesize a move constructor for class B, even though it's deleted. 
Therefore, we cannot copy or move objects of type `B`. 

**If a class derived from B wanted to move or copy it's objects, that class would have to define its own version of these constructors**. 
Of course, as the base class `B` is not supporting the moving and copying of its objects, the derived class would have to figure that out as well. 

Mostly, if a base class does not have a default, copy, or move constructor, then the derived classes will usually not either. 
If the derived classes do, then, that function will have to work on the base class as well, as we won't move to the base class if it isn't defined or synthesized. Go below in order to see that.

#### Move Operations and Inheritance
Most base classes define a virtual destructor. 
As a result, the base classes generally do not get synthesized move operations. 
Lack of a move operation in a base class suppresses synthesized move for its derived classes, base classes ordinarily should define the move operations if it is sensible to do so. 
If we define move operations, we must also explicitly define the copy versions as well. 
```
class Quote{ 
public: 
	Quote() = default; // memberwise default initialize
	Quote(const Quote&) = default; // memberwise copy
	Quote(Quote&&) = default; // memberwise move ctor
	Quote& operator=(const Quote&) = default;
	// copy assign
	Quote& operator=(const Quote&&) = default; 
	// move assign
	virtual ~Quote() = default;
	// other members as well
};
```
#memberwisecopy
AKA, shallow copy or bitwise copy, refers to a process where each member of an object is copied individually to another object of the same type. Copying values of members from one member to another. 

Classes derived from `Quote` from above, will automatically obtain synthesized move operations as well, unless they have members that otherwise preclude move (const members, members that they themselves don't have a move). 

#### Derived-Class Copy-Control Members
Everything that happens in the derived class, eg. copy/move/initializing etc. whatever we do to the derived members, we have to do to the base class members as well. 
A derived-class assignment operator must assign the members in the base part of the derived object. 

The destructors of a derived class are only responsible for destroying the resources allocated by the derived class. The base class destructor will be called implicitly as well.
Remember following the reverse of the construction. 

> When a derived class defines a copy or move operation, that operation has to be responsible for copying or moving the entire object, including base-class members. 

You can do this by just calling the `Base` class move constructor as well. 

```
Base(Base&& other) noexcept{ 
	cout ,< "Base move constructor" << endl;
}


Derived(Derived&& other) noexcept : Base(std::move(other)){ 
	cout << "Derived Move Constructor" << endl;
}
```

#### Defining a Derived Copy or Move Constructor
#derivedmove
#derivedcopy
When defining the copy and move operations in the derived, we're fine to just use the corresponding base-class constructor to initialize the base part of the object. 
```
class Base{ /* ... */ };
class D : public Base{ 
// by default, the base class default constructor initializes the base part of an object

// to use the copy or move constructor, we must explicitly call that

// constructor in the constructor initializer list

	D(const D& d) : Base(d) // copy the base members
		/* initializers for members of D */ {/*...*/}
	D(D&& d) : Base(std::move(d)) // move the base members
		/* initializers for members of D */ {/*...*/}
}
```
The initializer `Base(d)` passes a `D` object to a base-class constructor. 
`Base` might have a constructor that takes in a parameter of type `D`. 

Most likely `Base(d)` will match the `Base` copy constructor. 
As here we will find that `d`, of type `D`, will be bound to `Base&` parameter in that constructor. 
Passing `d` of type `D` here will use `Base(d)` matching the ordinary `Base` copy constructor. 
The `Base` copy constructor will therefore copy the base part of `d` into the object that is being created. 
Had the initializer for the base class been omitted, 
```
// probably incorrect defintion of the D copy constructor
// base-class part is default initialized, not copied
D(const D& d) /* member initializers, no base-class initializer*/
{ /* ... */ }
```
here the `Base` default constructor would be used to initialize the base part of a `D` object. 

I think the idea here is that the copy constructor of the derived class will invoke, implicitly the copy constructor of the base class. 

```
Derived::Derived(const Derived& other) : Base(other) { /* ... */}
```

If the base class has a copy constructor like this :  `Base::Base(const Base&)`, it will be auto copied by the derived class, as shown in the book and above. Ensures that the base class subobject of the derived class will be copied properly.




Remember that Base Ctor will be used before any Derived Ctor. 
The code below will give you a good example of how it all works together. 

It will be the most simple of classes. 

```
class Base {
public: 
	int value;
	Base() : value(0) { printf("Base Ctor default\n"); }
	Base(int a) : value(a) { printf("Base Ctor with int\n"); }

	Base(const Base& other) : value(other.value) { printf("Base Copy Ctor\n"); }

};

class Derived : public Base {
public: 
	string name; 
	Derived() : Base(), name("Default") { printf("Derived Ctor default\n"); }
	Derived(string n) : Base(), name(n) { printf("Derived Ctor with string\n"); }
	Derived(string n, int a) : Base(a), name(n) { printf("Derived Ctor with int and string\n"); }
	Derived(const Derived& other) : Base(other), name(other.name) { printf("Derived Copy Ctor/ using Base"); }

	// Derived(const Derived& other) : name(other.name){}
	// This here, if the only copy constructor will call the 
	// Base Default constructor in order to construct the base
	// subobject of the derived object.
};

int main(){
	Derived nD("Hugo", 3);
	Derived f(nD);
}
```
![[Pasted image 20240508161343.png]]
This is the output, which is pretty straightforward. 
The Base ctor goes first, after being called by the Derived Ctor, this inits the Base subobject or our derived object.
So when constructing, the base constructors go first, then the derived objects, remember to call the base ctors within the derived ctors. 

HOWEVER, if we don't call the base copy constructor here, using `Base(other)` where `other` is `const Derived&`, then the `Derived` copy constructor will just call the `Base` Default Constructor. 
> If no Base Copy Constructor called in Derived Copy Constructor, Base Default Constructor will be used. 

Unfortunately we can't have both copy constructors, so just if you dont' want the base part to be default constructed, then we have to specify that we pass up to the base copy constructor. 

This is just a weird thing to want, and honestly, if we wanted to copy an object and default the base subobject's, then maybe we should do that in a particular `operator=`, if you ever did want to do it, which you probably don't, or just have a `Base` inherited function that resets the base values? 

> By default, the base class default constructor initializes the base-class part of the derived object, this is the base rule. If we want to copy or move the base-class part, we must explicitly use the copy (or move) constructor for the base class in the derived's constructor initializer list. 

The way to think about it is, if we need to create a Base subobject part, then the Base class will just use the default constructor, unless we tell it to do otherwise. 

#### Derived-class Assignment Operator
The same thing is true, we just have to be explicit over which Base ctor we want to use, rather than letting the compiler just use the base default constructor. 
The assignment operator is just the same: 
```
// Base::operator=(const Base&) is not invoked automatically
D& D::operator=(const D& rhs)
{ 
	Base::operator=(rhs); // assigns the base part
	// now assign the members in the derived class
	// handle self-assignment 
	// free existing resources if we need to
	return *this;
}
```
The `Base::operator=(rhs)` will always be callable, whether or not that function is defined in `Base` or not, it will just use the compiler synthesized one if we don't define it ourselves. 


## Derived-Class Destructor
Remember that data members are implicitly destroyed after the destructor body completes. 
Unlike everything else we have here, the **derived destructor is only responsible for the derived members, NOT for any members of the base class**. 
```
class D : public{ 
// usual members
public: 
	// Base::~Base invoked automatically
	// unlike other methods eg. assignment, copy construction
	~D(){ /* do what you need to */ }
};
```
Remember that objects are destroyed in the opposite order from which they are constructed; destructors only need worry about their own subobjects. 

#### Calls to Virtuals in Constructors and Destructors
While the base class constructor is doing its thing, the derived subobject is uninitialized. 
Derived objects are destroyed in reverse order, therefore, the base class destructors is running last, the derived subobject will be destroyed at that point. The object will be "incomplete" while running the base destructor. 

To accomadate, the compiler treats the object as if its type changes during the construction and destruction. 
While the object is being constructed it is treated as if it has the same class as the constructor, calls to virtual functions will be bound as if the object has the same type as the constructor itself.

For destructors ditto, the binding applies to virtual called directly or that are called directly or that are called indirectly from a function that the constructor (or destructor) calls. 

If we had in the base constructor, a function call to a Derived class function, that utilised members of the derived object, then it would crash, or be super super undefined.
Those members and values don't even exist yet. 

> If a constructor or a destructor run a virtual, the dynamic type of the object is compiled so that it is the type of the Constructor or Destructor. Therefore if a "derived" object is within the base constructor, it will be treated as a base object. 


[[Inherited Constructors]]

###### My Own Notes; 
When we construct a derived object, we are constructing the base part of that object first, using the base class ctor, then we do the derived class constructor. 
```
class Base{ 
public: 
	Base() = default;
};

class Derived{ 
public: 
	Derived() = default;
};

int main() { 
	Derived d; 
	// Will use the Base ctor first
	// Then use the Derived ctor secondly

}
```


```
struct Base {
    int b_value;
    Base(int a) : b_value(a) { cout << "Called Base Constructor" << endl; }
    Base() : Base(0) {}
	~Base() { cout << "Base Destructor" << endl; }
};

struct Derived : public Base {
    int d_value; 
    Derived() : Derived(0, 0) {}
    Derived(int a, int b) : Base(a), d_value(b) { cout << "Called Derived Constructor" << endl; }
	~Derived() { cout << "Derived Destructor" << endl; }
};

int main(){
    Derived a(10, 10);
```

Destructors work the opposite way, destroying the derived object first. 
Then moving up to the base object. 
The output here is: 
```
Called Base Constructor
Called Derived Constructor

Derived Destructor 
Base Destructor
```

```
#include <iostream>

class Base {
public:
    Base(int val) : value(val) {}
    Base(const Base&) = delete; // Deleting the copy constructor
    int getValue() const { return value; }
private:
    int value;
};

class Derived : public Base {
public:
    Derived(int val, int derivedVal) : Base(val), derivedValue(derivedVal) {}
    Derived(const Derived& other) : Base(other), derivedValue(other.derivedValue) {}
    int getDerivedValue() const { return derivedValue; }
private:
    int derivedValue;
};

int main() {
    Derived d1(5, 10);
    Derived d2 = d1;
    std::cout << "Base value in d2: " << d2.getValue() << std::endl;
    std::cout << "Derived value in d2: " << d2.getDerivedValue() << std::endl;
    return 0;
}
```
I'm not sure if this actually works tbh. 
#movesynthesizing
A move operation is not defined if we : 
1. User-defined copy constructor or copy assignment. Both move constructor and move assignment will not be synthesized here. 
2. User-declared destructor : if you want the move operations, then you need to define them explicitly. 
3. If we define a move constructor or a move assignment operator, the compiler will not synthesize the other one, if you define one, define the other. 
4. If a base class has a non-trivial move constructor or move assignment operator, the derived class will not inherit these operations, The derived class will need to define its own move operations if explicitly required. 
5. Presence of certain types of members, if a class has members that do not support move operations (eg. const members, members with a deleted move constructor or move assignment operator), the move operations will not be synthesized. 
