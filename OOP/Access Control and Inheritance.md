#### `protected` Members
#protected
Sharing with derived class, but protecting from general access. 

It's a nice blend of `public` and `private`:
- Like `private`, `protected` members are inaccessible to users of the class. 
- Like `public`, `protected` members are accessible to members and friends of the classes derived from this class. 

`protected` has another very important property: 
-  A derived class member or friend may access the `protected` members of the base class only through a derived object. The derived class has no special access to `protected` members of base-class objects. 

To understand this a little better: 
```
class Base{ 
protected: 
	int prot_mem; // protected member
};

class Sneaky : public Base{ 
	friend void clobber(Sneaky&); // can access Sneaky::prot_mem
	friend void clobber(Base&); // can't access Base::prot_mem
	int j; // j is private by default
};

// Clobber can access the private and protected members in Sneaky object
void clobber(Sneaky& s) { s.j = s.prot_mem = 0; }


// error : Clobber can't access the protected members in base: 
void clobber(Base& b) { b.prot_mem = 0; }
```
Members and friends of a derived class can access the `protected` members *only* in base-class objects that are embedded inside a derived type object; they have no special access to ordinary objects of the base type. 


#### `public, private, protected` Inheritance
Consider: 
```
class Base{ 
public: 
	void pub_mem(); // public member
	
protected:
	int prot_mem; // protected member

private: 
	char priv_mem; // private member
};

struct Pub_Derv : public Base{ 
	// ok; derived classes can access protected members
	int f() { return prot_mem; }
	// Error : private members are private to derived classes
	char g() { return priv_mem; }
};

struct Priv_Derv : private Base{ 
	// private derivation affect access in the derived class
	int f1() const { return prot_mem; }
};
```

The access modifier and specifier in the derived class, don't really do anything for the members that it inherits from the base class. 
It affects how the users of the derived class - **including the derived classes derived from that class** - have to the members inherited from `Base`. 

```
Pub_Derv d1; // members inherits from Base are public
Priv_Derv d2; // members inherited from Base are private
d1.pub_mem(); // pub_mem is public in the derived class
d1.pub_mem(); // error pub_mem is private in the derived class
```
![[Pasted image 20240505114543.png]]

Look up any picture here and it will probably be correct lol. 
![[Pasted image 20240505114619.png]]

The main idea is that the members that have access modifiers, get further modified moving into the next class/derived class. 
So if a have a public member in a base class ; if the inheritance is protected, then the public member becomes protected in the derived class. 

#### Accessibility of Derived-to-Base Conversion
Whether the derived-to-base conversion is accessible depends on which code is trying to use the conversion and may depend on the access specifier used in the derived class' derivation: 
Assuming `D` inherits from `B`: 
- User code can use the derived-to-base conversion *only* if D inherits publicly from B. User code could not use conversion if D inherits from B in a private or protected manner. 
- Member functions and friends of D can use the conversion to B regardless of how D inherits from B. The derived-to-base conversion to a direct base class is always accessible to members and friends of a derived class. 
- Member functions and friends of classes derived from D may use the derived-to-base conversion if D inherits from B using either `public` or `protected`. Such code may not use the conversion if D inherits privately from B. 


**Derived-to-Base**: 
Names the process of converting a pointer or reference of a derived class type to a pointer or reference of a base class type. 
Safe and implicit as every object of a derived class is also an object of it base class: 
```
class Base{ 
public: 
	void display() { 
		//...
	}
};
class Derived : public Base{ 
public: 
	void show() { 
		//... 
	}
};

int main(){ 
	Derived derivedObj;
	Base* basePtr = &derivedObj;
	basePtr->display();
	basePtr->show(); // Error if the function is not virtual
}
```

For any given point in code, if a `public` member of the base class would be accessible, then the derived-to-base conversion is also accessible, and not otherwise.

>**Class Design and `protected` Members**
>We can think of a class having two kinds of users: ordinary users and implementers. 
>Ordinary users will write code that uses objects of the class type; such code can access only the `public`(interface) members of the class. 
>Implementers write the code contained in the members and friends of the class. 
>The members and friends of the class can access both the `public` and `private` (implementation) sections. 
>Another idea, is that thanks to the `protected` type, we can let derived classes use those members. They, and `private`, will remain inaccessible to users. 
>Base class, using public interface, we can separate using access for implementation from our derived classes and those that remain accessible only to the base class and its friends. 
>Using `protected` if an implementation member should be changed or altered in the derived class. 
>Otherwise implementation members should be `private`.

#friendship
### Friendship and Inheritance
Remember that friendships is not transitive. Each friendship is limited to the class it's declared with. 

```
class Base{ 
	// added friend declaration; other members are standard
	friend class Pal; // pal has no access to classes derived from Base
};

class Pal{ 
public: 
	int f(Base b){ return b.prot_mem; } // ok Pal is a friend of Base
	int f2(Sneaky s){ return s.j; }
	// error as Pal not a friend of Sneaky
	// access to a base class is controlled by the base class
	// even inside a derived object
	
	int f3(Sneaky s) { return s.prot_mem; }
	// ok as Pal is a friend
};
```

The last might seem a little odd, but it follows directly from the notion that each class controls access to its own members. 
`Pal` is a friend of `Base`, so `Pal` can access the members of `Base` objects. 
That access includes access to `Base` objects that are embedded in an object of a type derived from `Base`. 
When a class makes another a friend, it is only that class to which friendship is granted, not to any derived classes that come from that "base" class. 
It's just a one off branch, it doesn't go down the hierarchy. 

Therefore: 
```
// D2 has no access to protected or private members in Base
class D2 : public Pal{ 
public: 
	int mem(Base b)
		{ return b.prot_mem; } // error: friendships doesn't inherit
}
```
#### Exempting Individual Members
Sometimes we need to change the access levels of a name that a derived class inherits: 
We do so using `using` declarations: 
```
class Base{ 
public: 
	size_t size() const { return n; }
protected: 
	size_t n;
};

class Derived : private Base{ // private inheritance
public: 
	// maintain access levels for members related to the size of the object
	using Base::size; 
protected: 
	using Base::n;
};
```
The `using` here alters the type of inheritance of the specified members. Outside of the class-wide inheritance at the beginning of the class. 

**Here, users of `Derived` can access the `size` member, and classes subsequently derived from `Derived` can access `n`**. 
A `using` declaration can name any accessible member of a direct or indirect base class (not `private`, declared `private` in the base class). 

> A derived class may provide a `using` declaration only for names it is permitted to access. 


So if the class utilising `using` doesn't even have access to the member it is changing the access of, then it can't change that access level. So really it's just a toss up between `public` and `protected`. 

Here, in the example above, we are taking the `size()` making it private upon inheritance, and then making it `public` again, by utilising `using`. 

```
class Derived : accessspec_1 Base{ 
accessspec_2: 
	using MemberFromBase; 
	// Now MemberFromBase is level accessspec_2
	// Provided that MemberFromBase is not private in Base
}
```

#### Default Inheritance Protection Levels
#defaultinheritancelevels
Just like `struct, class` with their default access specifiers. 
By default, a derived class defined with the `class` keyword has `private` inheritance; a derived class defined with `struct` has `public` inheritance: 
```
class Base{ /*...*/ };
struct D1 : Base { /*...*/ }; // Public inheritance by default
class D2 : Base { /*...*/ }; // private inheritance by default
```
There are no other distinctions now (until there magically will be later on lol) between struct and class. 

We should still declare for clarity, regardless of the default inheritance. 


#### Class Scope under Inheritance
Classes have their own scope, in which their members are defined. 
Under inheritance, the scope of the derived class is nested, inside the scope of its base classes. 
If a name is unresolved within the scope of the derived class, the enclosing base-class scopes are searched for a definition of that name. 

This does mean that we can use members of the base class of though they are members of the derived class, without this nesting scope, this wouldn't be possible.

eg. when we write: 
```
Bulk_quote bulk; 
cout << bulk.isbn();
```
The search for `isbn()` starts in `Bulk_quote` as that is the first class that we have called `isbn()` on. 
If not wholly found here, we move onto the `Disc_quote`, here, the name is still not found. 
`Disc_quote` is derived from `Quote`, that class is searched next. Therefore the `isbn()` is resolved in the class `Quote`. 

#### Name Lookup Happens at Compile Time
The static type of an object, reference, or pointer determines which members of that object are visible. 
Even when static and dynamic parts of the object are different. 

The static type determines what members can be used. 

Adding a member to `Disc_quote` that returns a `pair`. 

```
class Disc_quote : public Quote{ 
public: 
	std::pair<size_t, double> discount_policy() const { 
	return {quantity, discount};} 
	// other members as before
};
```

We can use this only thought an object, pointer, or reference of type `Disc_quote` or of a class derived from `Disc_quote`: 

```
Bulk_quote bulk; 
Bulk_quote* bulkP = &bulk; // static and dynamic types are the same

Quote* itemP = &bulk; // Static and dynamic types differ

bulkP -> discount_policy(); // bulkP has type Bulk_quote*
itemP -> discount_policy(); // Error
// itemP has type Quote*
```

Remember that when we have a pointer of Base type to a Derived type, only the Base part of the derived object is visible. 
Now if the virtual function is declared in the base class, this is possible, as the virtual aspect on the vtable, will allow it to find the derived version, that overrides. 
Here the `Quote*` means that the search for the `isbn()` function will start in the `Quote` class. 


#### Name Collisions and Inheritance
A derived class can reuse a name defined in one of its direct or indirect base classes. 
Names defined in an inner scope (derived classes too) hide uses of that name in the outer scope (eg. a base class). 

```
struct Base{ 
	Base() : mem(0) {}
protected: 
	int mem;
};

struct Derived : Base { 
	Derived(int i) : mem(i) {} // initializes Derived::mem to i
	// Base::mem is default initialized 

	int get_mem() { return mem; }
	// returns Derived::mem
protected: 
	int mem; // hides mem in the base
};
```
The reference to `mem` from `get_mem` is resolved to the name inside `Derived`. 
Were we to write: 
```
Derived d(42); 
cout << d.get_mem() << endl; // prints 42;
```
So just remember that the derived class is in the inner scope, it works just the same as this: 
```
int i = 42; 
{ // new scope 
	int i = 500;
	cout << i << endl; // output 500
}
cout << i << endl; // output 42
```
A derived class member with the same name as a member of the base class hides direct use of the base class member.

#### Using Scope Operators to Use Hidden Members
Like normal, we can use the hidden base class member by using the scope operator: 
```
struct Derived : Base { 
	int get_base_mem() { return Base::mem; }
	//... 
}
```

This overrides the normal (outwardly moving) lookup, and directs the compiler to look for `mem` starting in the scope of class `Base`. 

> **Good Practice**; a derived class should usually not reuse names defined in its base class, other than inherited virtual functions. 

###### Name Lookup and Inheritance
Given the call `p->mem()` (or `obj.mem()`): 
1. First we have to see the static type of `p` or `obj`, as we're calling a member, that type must be a class type. 
2. Look for `mem` in the class that corresponds to the static type of `p` or `obj`. If `mem` is not found, look in the direct base class and continue up the chain of classes until `mem` is found or the last class is searched. If `mem` is still not found in the class or its enclosing base classes, then the call will not compile. 
3. Once `mem` is found, do normal type checking to see if this call is legal given the definition that was found. 
4. Assuming the call is legal, the compiler will generate code, which varies depending on whether the call is virtual or not: 
		- If `mem` is virtual and the call is made through a reference or pointer, then the compiler generates code to determine at run time which version to run based on the dynamic type of the object. 
		- Otherwise, if the function is nonvirtual, or if the call is on an object (not a ref or pointer), the compiler generates a normal function call. 

#### As Usual, Name Lookup Happens before Type Checking 
Functions in a derived class do not overload members defined in its base class(es). 
The derived member will hide the base class member, within the scope of the derived class. 
The base member is hidden even if the functions have different parameter lists: 
Like so: 
```
struct Base{ 
	int memfcn(); // will have different parameters to Derived
};

struct Derived : Base { 
	int memfcn(int); // hides memfcn in the base
};

Derived d; Base b;
b.memfcn(); // Calls Base::memfcn
d.memfcn(10); // Calls Derived::memfcn
d.memfcn(); // Error memfcn with no args is hidden
d.Base::mcmfcn(); // Ok calls Base::memfcn
```
The declaration in Derived hides that in Base. 

`d.memfcn()` here is illegal. To resolve this the compiler looks for the name `memfcn` in Derived. 
The search stops, weirdly, as the names match, however, that version of `memfcn` requires an `int` , therefore we run into some errors. 


#### Virtual Functions and Scope
If the base and derived members took arguments that differed from one another, there would be no way to call the derived version through a reference or pointer to the base class. It would just call the base version all the time, it would also be completely missing the point of polymorphism too. 

eg. 
```
class Base{ 
public: 
	virtual int fcn(); 
};
class D1 : public Base{ 
public: 
	// hides fcn in the base; this fcn is NOT VIRTUAL
	// D1 inherits the definition of Base::fcn()
	int fcn(int); 
	virtual void f2();
};
class D2 : public D1{ 
public: 
	int fcn(int); // nonvirtual function hides D1::fcn(int)
	int fcn(); // Overrides virtual fcn from Base
	void f2(); // overrides virtual f2 from D1
};
```
In order to override a virtual function, the signature has to be the same otherwise we run into some issues. 

D1 does not override the virtual `fcn` from `Base`, they have different parameter lists. It hides that `fcn` from base instead. 
Effectively `D1` has two functions that named `fcn`: D1 inherits one from Base, and defines its own, the nonvirtual one has no parameter list. 
#### Calling a Hidden Virtual through the Base Class
Given the classes above, let's see how we might call all of these functions: 
```
Base bobj; D1 d1obj; D2 d2obj;
Base* bp1 = &bobj, *bp2 = &d1obj, *bp3 = &d2obj;
bp1->fcn(); // virtual call, will call Base::fcn at run time
bp2->fcn(); // virtual call will call Base::fcn at run time
bp3->fcn(); // virtual call will call D2::fcn at run time
D1 *d1p = &d1obj; D2 *d2p = &d2obj;
bp2->f2(); // error Base has no member named f2
d1p->f2(); // virtual call, will call D1::f2() at run time
d2p->f2(); // virtual call, will run D2::f2() at run time
```
`fcn` from base is virtual, meaning that we will decide on the dynamic typing which function is called.  
`bp2->f2()` will look in base first, if it finds a virtual function there, then it will dynamically move to the type of the actual object. 
In the D1 (`bp2`) objects case, it did not override the function, therefore we move to base. It doesn't have the same parameters. 
The next three calls are all made through pointers with different types. 
Each pointer points to one of the types in this hierarchy, remember that if the function is not virtual, then we will call based on the static type of the pointer. 
The first being illegal as there is no `f2()` in `Base`. 
The fact that the pointer happens to point to a derived object is irrelevant.  

Let's take a look at a few more: 
```
Base* p1 = &d2obj; D1* p2 = &d2obj; D2* p3 = &d2obj;
p1->fcn(42); // Error base has no version of fcn that takes an int

p2->fcn(42); // statically bound, Calls D1::fcn(int)
p3->fcn(42); // statically bound, calls D2::fcn(int)
```

In each call, we are pointing to an object of d2 , however, the dynamic type doesn't matter at all when we call nonvirtual functions. 
The version that is called depends only on the static type. 

#### Overriding Overloaded Functions
As with any function, virtual and not, they can be overloaded. 
A derived class can override zero or more instances of the overloaded functions it inherits. 
If a derived class wants to make all the overloaded versions available through its type, then it must override all of them or none of them. 
Sometimes we override some of the functions, but not all. 
It's tedious to override every base-class version in order to override the ones that the class needs to specialize. 

Instead of doing that, we can just a form of `using` declaration for the overloaded member. 

This will only use a name; it may not specify a parameter list; 
A `using` declaration for a base class member function adds all the overloaded instances of that function to the scope of the derived class. 
Having brought all the names into its scope. 
Remember when we had that weird thing in the earlier chapters where it was telling us to do `using std::swap`.What we are doing is bringing that name into scope. It will hide any of the `swap` functions that are outside the scope. 
If you look at this code: 
```
auto i = []()->int { return 42; }
{ 
	auto i = []()->{ return 500; }
	cout << i() << endl;// outs 500
}
cout << i() << endl; // outs 42
```
Here if the inner `i` didn't hide the outer `i`, then we would have two perfect functions, so what would the compiler do, it's easier just to hide the outer. 
Now also, remember that thing where we had to `using std::swap`. 
The main thing that it does is if I want to use `std::swap` I don't have to precede it with `std::` every time. 
We can just bring it into scope. That's why when we write `using std::cout` in the global scope, then we can just write `cout` without the `std::` every time. 

I can do something like this: 
```
namespace Testing{ 
	void PrintOut() { 
		cout << "Testing Printout" << endl;
	}
}

void PrintOut(){ 
	cout << "Printout in the global scope" << endl;
}

int main(){ 
	PrintOut(); // Printout in the global scope
	using Testing::PrintOut;
	PrintOut(); // Testing Printout
}
```
So that's how it's going to work here as well. 
Having brought all the names into scope, the derived class needs to define only those functions that truly depend on its type. 
Using the inherited definitions for the others. 
The normal rules for a `using` declaration inside a class apply to names of overloaded functions. Every overloaded instance of the function in the base class must be accessible to the derived class. 
The access to the overloaded versions that are not otherwise redefined by the derived class will be the access in effect at the point of the `using` declaration, meaning that if we have under `public` the `using someFunct`, then the funct will be at `public` level. 

ChatGPT says this is a bit tricky: 
```
class Base{ 
public: 
	virtual void print(){ 
		cout << "Base Print(int)" << endl;
	}

	virtual void print(int x) { 
		cout << "Base print(int): " << x << endl;
	}
};

class Derived : public Base{ 
public: 
	// using Base's print() function
	using Base::print;
	// this includes print(int)

	// override Base's print() function
	void print() override{ 
		cout << "Derived Print()" << endl;
	}
};

int main(){ 
	Derived d; 
	d.print(); // Derived's print()
	d.print(5); // Call's Base's print(int)
}
```
###### Virtual 
#virtual 
So declaring a function as virtual just means that it allows dynamic dispatch, which means that the function to be called is determined at runtime based on the dynamic type of the object, not the static type. 
When we see virtual we know that we can override that function (in derived class, as the scope thing comes into effect here) with the same signature in a derived class. 
Allowing for polymorphism. Where a derived object can be treated as if it were an object of its base class. 
There is the idea of vtable creation : when a class has virtual functions, the compiler will create a virtual table (vtable) for that class. Created at compile time, contains pointers to virtual functions of that class. 
Each object of a class has a hidden pointer, often called a vptr (virtual table pointer), created by compiler and set up at run time. 
When a virtual function is called through a base class pointer or reference, the actual function gets called is determined at runtime based on the dynamic type of the object, not the static type (the type of the pointer). 
The compiler then uses the vptr to find the correct vtable, and then the vtable is used to find the address of the appropriate function to call. 

```
class Base{ 
public: 
	virtual void show() { 
		cout << "Base class show function" << endl;
	}
};

class Derived : public Base{ 
public: 
	void show() override{ 
		cout << "Derived class show function" << endl;
	}
};


int main{ 
	Base* ptr = new Derived();
	ptr->show(); // calls on dynamic type, so derived
	delete ptr; // remember this
	return 0;
}
```
Really the use of virtual is so that we can have late binding, dynamic dispatch, dynamic binding, whatever you want to call it. 



```
struct Base{ 
	int value; 

	Base() = default;
	
	virtual void PrintOut(){ 
		cout << "Base Printout Called" << endl;
	}
};

struct Derived : public Base{ 
	Derived() = default;

	void PrintOut override { 
		cout << "Derived Printout Called" << endl;
	}
};

int main() { 
	Base* bobj = new Derived; 
	bobj->PrintOut(); 
	// will output the dynamic objects call, 
	// "Derived Printout Called" will be output
}
```

If the functions are not virtual
```
struct Base{ 
	void PrintOut(){ ... }
	//... 
};
struct Derived : public Base{ 
	void PrintOut() { ... }
};
```
Here we get `"Base Printout Called"`. 
