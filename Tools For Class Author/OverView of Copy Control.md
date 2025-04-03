Remember that we have to use mutable with lambdas in order to make sure that we change the values that we have captured into it. When you capture variables into a lambda, they become `const` by nature (that the lambda object is `const` itself, we will never need to the change the function itself, and by definition, our variables that pass into it). 
eg. 
If we pass everything by reference, then there's nothing to really worry about, however if we pass everything in by value `=` in the capture list, then we have to say mutable. 
```
int a = 10, b = 200;

auto fR = [a, b]() mutable { 
	a = 2000;
	return a < b; }; // without mutable, this would fail 
// if we pass [&a, &b] then we also don't need mutable
```
![[Pasted image 20240422123722.png]]

Copy Constructor:  first parameter is a reference to the class type, any other params have default values
```
class Foo{
public: 
	Foo(); // default constructor
	Foo(const Foo&); // copy constructor
};
```

Implicit needed as implicit copying is quite common.

Synthesized copy constructor -> uses memberwise copies. 
Element's own copy constructor when passing over. 

Synthesized copy constructor will just copy each element of an array. 

Usually synthesized like this: 
```
Simple::Simple(const Simple& orig) : value(orig.value) {}
```

That's the general idea. 

When we use copy initialization, we are asking for RHS to be copied into the creation of the other object. 
Copy constructor used if we :  `Simple a(b);` and `Simple a = b;`

Copy initialization ordinarily uses the copy constructor, except when we move. 

```
Simple a(10);
Simple b(a);
Simple c = b; // These both use copy constructor
```
Remember the different between initialization and assignment (initialization is assigning to a new object, assignment is changing values of the an already established object). 
`a = b` will use the Copy Assignment `operator=` idea here. 

Copy initialization not only when using an `=`
- pass an object as an argument to a parameter of nonreference type
- Return an object from a function that has a nonreference return type
- Brace initialize the elements in an array or the members of an aggregate class

The call to a copy constructor will have to be a reference, otherwise we would never escape the copy constructor, we would have to copy due to non-reference param type. 
`Object obj2(obj1);` if the copy constructor doesn't take in a reference, then the copy constructor will be used to copy `obj1` into the function body, and that will just go on forever.

The compiler can bypass the Copy Constructor: 

Assignment operators should normally return a reference to their left-hand operand. 

```
Simple& operator=(const Simple& rhs)
{ 
	value = rhs.value; // calls the int::operator=
	return *this;
}
```

Members are destroyed in the reverse order that they are initialized, meaning that if we require one member for another for initialization, the destroying, will therefore mostly require the reverse. 

You might want to save a copy of a destroyed object, not sure why, but you could do that in the destructor. 

Not called when a reference or a pointer to an object goes out of scope. 

Members are destroyed implicitly after the body of the destructor. 

Remember the rule of three/five./ 

Deleted function - declared but cannot be used in any way, it has to be declared so we know that if it comes up again, it's deleted. 

`=delete` has to be in the first declaration of the function.

Never `=delete` the destructor. 

If a member has deleted copy control functions, then the object they are a part of will have that as well. 

Default constructors will struggle if any of the members do not have default constructors too. 
So if we have `const` member functions, then we need to write our own copy ctor and copy assignment. 

Our copy-control members are synthesized as deleted when it is impossible to copy, assign, or destroy a member of the class. The compiler just doesn't want to guess and get into it. 

Classes that act like Values
Classes that act like Pointers

Each object, has its own values, not just pointers to more global values. 

Not copying, meaning that we have pointer-like properties, means that we have to copy the pointers, each object will therefore have its own pointers. 

Remember that we to make sure that our assignment operator have to ensure that if we assign the object to itself, it works there too. 

Reference counter can be put in dynamic memory, and then each object has a pointer. 

Then when the last object of that type is destroyed, then we get rid of the `size_t` that we have in dynamic memory. 

```
HasPtr::~HasPtr()
{ 
	if(--*use == 0){ 
		delete ps; // delete the string
		delete use; // and the counter, this would remain of course
		// if there are other objects that use it. 
	}
}
```

Then in initialization of an object

Writing our own `swap` function: 
```
class Hasptr{ 
	friend void swap(HasPtr&, HasPtr&);
}

inline
void swap(HasPtr& lhs, HasPtr& rhs)
{ 
	using std::swap; // make sure we aren't calling recursively
	swap(lhs.ps, rhs.ps); // swap the poitners, not the string data
	swap(lhs.i, rhs.i); // swap the int numbers
}
```
Remember ADL(argument dependent lookup). 
So now that it is a friend, then ADL will use it before as overloaded `std::swap`, as we look in the classes when we use ADL. 

Imagine that we had a class that contains another class, and we use `swap`, on the outer class, it will not use the inner class `swap`, if the outer class hasn't defined `swap`. It will use `std::swap`. 
This is true of a lot of overloaded functions.

Then if we define the outer version of `swap`: 
```
void swap(Foo &lhs, Foo &rhs)
{ 
	// WRONG 
	std::swap(lhs.h, rhs.h);
	// swap other members of type Foo
}
```

So even if we say `using std::swap`
```
void swap(Foo &lhs, Foo &rhs)
{ 
	using std::swap; 
	swap(lhs.h, rhs.h); // will still use HasPtr version of swap
	// due to ADL
	// Swap other members of Foo
}
```

So argument specific version of swap here is going to be called instead. 


Moving objects: 

Something to note here about rvalues and lvalues: 
```
string&& f = "Hello World";
string& a = f;
PrintRvalue(f); // here, f is an lvalue
```

YOU CANNOT DO SOMETHING LIKE THIS: 
```
string& f = "Hello World"; // cannot do this
```
This is because a `string&` cannot be initialized by a `const char[12]`. 
Obviously this is wrong as well: 
```
string a("Hello World");
string&& f = a; // cannot bind an rvalue reference to an lvalue
```

Lvalues persist; Rvalues are ephemeral. 

You can do something like this: 
```
string a("Hello World");
string && f = a + " Done";
```

```
int && rr1 = 42; // fine 
int && rr2 = rr1; // not fine, rr1 is an lvalue
```

A variable is an lvalue: 

`std::move` will return rvalue from the variable in the arg. 

`std::move` is a better usage in order to not collide with other functions. 

