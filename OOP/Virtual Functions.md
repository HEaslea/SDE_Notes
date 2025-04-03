#oopoverview
#virtualfunctions
C++ dynamic binding happens when a virtual member function is called through a reference or a pointer to a base-class type. 

As we don't know which version of a function is called until run time. 
Virtual functions must **always** be defined. 

If we don't use the function, then don't define it (not sure what the book is getting at here). 

**We have to define eveqry virtual function**, regardless of whether it is used or not, as the compiler will have no way of knowing if it is called until run-time or not. 

> **Key Concept: Conversions among Types Related by Inheritance**
> There are three things that are important to understand about conversions among classes related by inheritance: 
> - The conversion from derived to base applies only to pointer or reference types
> - There is no implicit conversion from base-class type to the derived type. 
> - Accessibility might prevent derived-to-base conversions. 
> Although the automatic conversion applies only to pointers and references, most classes in an inheritance hierarchy (implicitly or explicitly) define the copy-control members. 
> We can often copy, move, or assign an object of derived type to a base object. 
> Copying, moving or assigned a derived-type object to a base-type object copies, moves, or assigns **only** the members in the base-class part of the object. 

[Website Explaining some Parts of It](https://medium.com/geekculture/c-inheritance-memory-model-eac9eb9c56b5)

Remember that when we declare a function as virtual, it goes on the vtable. 
Which stores function pointers for all the virtual functions. 
One vtable per class and all objects of the class have a vtable pointer to it. 

![[Pasted image 20240505001940.png]]

To note, that the derived vtable, will also have all the other virtual functions it created on there, after the virtual functions from the base class. 

It's essentially an array of function pointers. 

Virtual functions, get looked up on the object's vtable at runtime. 

This whole thing right here allows polymorphism. 

So why can a Base Pointer point to a Derived Object. 

All derived objects are a Base, but not all Base objects are a Derived. 
So if you have a Base pointer, you're only going to call functions that are declared in the Base class. 

Derived inherits all the functions and variables from Base, the Derived object will have access to all the functions that Base on it, this is a desired feature. 

In order for this to work, the order of the virtual functions and variables needs to be the same in Base and Derived objects. 

When a Base pointer looks at a Derived object, it only sees the base part of that object. 
![[Pasted image 20240505003139.png]]
To `b_ptr` is only looking at the base part of the object. 

Essentially, calling `bar1()` means that we are actually calling the first function pointer in the vtable. 

`b_ptr->bar1()` -> translates to -> `*((b_ptr -> vtable_ptr)[0](d_ptr)`. 

This becomes more noticeable in the code in Visual Studio that I have: 
```
class Base{ 
public: 
	int value; string name; 
	// Ctors are all normal here


	void BPrintOut(){ 
		cout << value << "   " << name << endl;
	}
};

class Derived : public Base { 
public: 
	string d_name;

	// Ctors are all normal here 

	void DPrintOut(){ 
		cout << value << "  " << name << "   " <<  d_name << endl;
	}
};

int main{ 
	Base* bObj = new Derived("Hugo");
	bObj->BPrintOut(); // fine as bObj is just the base part of the derived object, meaning that we only have access to the Base functions etc. 

	//
	bOjb->DPrintOut(); // ERROR! 
	// The compiler can't see the Derived Part of the derived object due to the pointer that we have. 
}
```


##### Calls to Virtual Functions *may be* Resolved at Runt Time
When a virtual function is called through a reference or pointer, the compiler generates code to *decide at run time*. 
The Function that is called is linked to the dynamic type, not the static type of the object bound to that pointer or reference, this is only for virtual functions here. 

If we have `Quote&` object, `item`, calling `net_price`. 
Because `item` is a `reference (Quote&)`, and because `net_price` is virtual, the version of `net_price` that is called depends at run time on the actual (dynamic) type of the argument bound to `item`. 

Remember that this is only true of `virtual` functions, if the `net_price` function was not virtual 

```
Quote base("0-201-83470-1", 50);
print_total(cout, base, 10); // calls Quote::net_price
Bulk_quote derived("0-201-82470-1", 50, 5, .19);
print_total(cout, derived, 10); // Calls Bulk_quote::net_price
```

>**It is crucial to understand that dynamic binding only happens when a <u><b>virtual function</u></b> is called through a pointer or a reference**

```
base = derived; // Copies the Quote part of derived into Base
// remember that it won't copy the Derived part

base.net_price(20); // Calls Quote::net_price
```

This call here will be bound at compile time, as there is no pointer or reference. 

From my own code here : 
```
Derived dObj(10, "Hugo", "D_Obj");
Base bObj = dObj; 
bObj.BPrintOut(); // Will output 10    Hugo
```
This is because the bas class ctor can have a `Derived&` passed into it. 

#polymorphism
> **Polymorphism in C++**: 
> Polymorphism just means "many forms". 
> Types related by inheritance are considered polymorphic types, they can use the "many forms" of these type while ignoring the differences among them. 
> The fact that the static and dynamic type of references and pointers can differ is what supports polymorphism entirely, without this there would be no polymorphism. 

> When we call a function defined in a base class through a reference or pointer to the base class, we do not know the type of the object on which that member is executed. 
> The object could be a base class object, or a derived class. 
> If the function is virtual, then the decision as to which function to run is delayed until run time. 
> The version of the virtual function that is run is the one defined by the type of the object to which the reference is bound or to which the pointer points. 

 > Calls to nonvirtual functions are bound at compile time. Calls to any function (virtual or not) on an object are also bound at compile time. 
 > The type of an object is fixed and unvarying - there is nothing we can do to make the dynamic type of an object differ from the static type here. 

> Virtuals are resolved at runt time *only* if the calls is made through a reference or pointer. Only in these cases can the dynamic type differ to its static type. 

##### Virtual Functions in a Derived Class
When a derived class overrides a virtual function, it may, but is not required to, repeat the `virtual` keyword. 
Once a function is declared as `virtual`, it remains `virtual` in all the derived classes. 

Of course the overridden virtual functions have to have the same parameter types. 
With one exception, the return type of a virtual in the derived class also must math the return type of the function from the base class. 

The exception applies to `virtuals` that return a reference (or pointer) to types that are themselves related by inheritance. 
That is `D` is derived from `B`, then a base class virtual can return a `B*` and the version in the derived can return a `D*`. 
Here we have to make sure that the derived-to-base conversion from `D` to `B` is accessible. 

#### The `final` and `override` Specifiers
If, in a derived class, we define a function that has the same name as a virtual in its base class, but with a different parameter list. 

The compiler considers such a function to be independent from the base-class function. 
In this case, and such, the derived version does not override the version in the base class. 
In practice, these declarations are a mistake - why would you really want to do this. 

We should specify `override` on a virtual function in a derived class. 
Doing so makes our intentions very clear and gives some aid to the compiler in order to help debug. 
The compiler will tell us we're being very silly if we define `override` and we're not actually overriding any function. 
```
struct B{ 
	virtual viod f1(int) const;
	virtual void f2();
	void f3();
};

struct D1 : B{ 
	void f1(int) const override; // f1 matches f1 in base 
	void f2(int) override; // ERROR
	// B has not f2(int) function

	void f3() override; // ERROR
	// f3 not virtual

	void f4() override; // ERROR again
	// B has no function named f4
};
```

We can also designate a function as `final`. Any attempt to override a function that has been defined as `final` will be flagged as an error: 
```
struct D2 : B{ 
	// inherits f2() and f3() from B and overrides f1(int)
	void f1(int) const final; // Derived classes can't override f1(int)
};

struct D3 : D2{ 
	void f2(); // Overrides f2 inherited from the indirect Base
	void f1(int) const; // Error D2 declared f2 as final
};
```
These words appear after the parameter list. After `const` as well. 

#### Virtual Functions and Default Arguments
The default arg will be used is the one defined by the static type through which the function is called. 

When a call is made through a ref or a pointer to base, the default argument will be those defined in the base class. 

The base-class arguments will be used even when the derived version of the function is run. 

In that case, the derived function will be passed the default args defined in the base-class version of the function. 

Therefore: 
> **Best Practice** here is to have all the default arguments as the same throughout the base and the derived. 

```
Base* bObj = new Derived("Hugo");
Derived* dObj = new Derived("New D");
bObj->PrintClass();  // Prints Derived version 
bObj->PrintDefault(); // uses base class default
dObj->PrintClass(); // Prints Derived version
```
All the functions here are overridden in the derived class. 
However, we still get the printed value of the default argument of that in the base class. 
However if I did this: 
```
Derived newD("d_name");
newD.PrintDefault(); // Here we use the default defined in Derived
```

#### Circumventing the Virtual Mechanism
In some cases, we want to prevent dynamic binding (early and late binding). 
We want the call to use a particular version of that virtual. We can use the scope operator in order to do that. 

```
// cals the version from the base class regardless of the dynamic type of baseP
double undiscounted = baseP->Quote::net_price(42);
```

Meaning we can even do something like this: 
```
Derived newD("d_name");
newD.Base::PrintClass(); // and this will work lol
```

Why should we need to circumvent this virtual mechanism? This is usual when we want to call the base class virtual function from the derived class. 
In such cases, the base class function might do work common to all types in the hierarchy. 
The versions defined in the derived classes would do whatever additional work is particular to their own type. 

**IF WE DO NOT USE THE SCOPE OPERATOR HERE, THEN IT WILL RESULT IN INFINITE RECURSION**. 

