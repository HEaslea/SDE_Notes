#swap
[[Copy Control]] [[Destructor]]
[[Destructor EARLY]]

Classes that manage resources often also define a function named `swap`. 

This is important for classes that we plan to use with algorithms, such algs call swap, whenever they need to exchange two elements. 

If a class defines its own swap, then algorithms will use that definition. 

```
HasPtr temp = v1; // make a temp copy of the value of v1
v1 = v2;  // assign the value of v2 to v1
v2 = temp; // assign the saved value of v1 to v2
```
This is a lot of memory allocation. Rather than allocating new copies of the `string`, we'd like `swap` to swap the pointers. 
```
string* temp = v1.ps; // make a temp copy of the pointer in v1.ps
v1.ps = v2.ps; 
v2.ps = temp; 
```

###### Writing our own `swap` Function
```
class HasPtr{ 
	friend void swap(HasPtr&, HasPtr&);
	// other members as before
};

inline
	void swap(HasPtr& lhs, HasPtr& rhs)
{ 
	using std::swap;
	swap(lhs.ps, rhs.ps); // swap pointers, not the string data
	swap(lhs.i, rhs.i); // swap the int members
}
```

Declare as a friend, to give access to `private` data. 

The `inline` is to optimize our code. The body of `swap` calls `swap` on each of the data members of the given object. 
Swap the pointers and then the int members. 


`swap` is never 100% necessary, however, it can be important for optimization for classes that allocate resources. 

There is an important subtlety, it is important that swap call `swap` and not `std::swap`. 
In `HasPtr`, we have built-in types for our members. 
There is no type-specific version of `swap` for the built-in types. 
In this case, these calls will invoke the library `std::swap`. 

However, if a class member that has its own type-specific `swap` function, calling `std::swap` would be an error. 
Assume that we had another class named `Foo` that has a member named `h`, which has type `HasPtr`. 
If we did not write a `Foo` version of swap, then the library version of `swap` would be used. 
As we've seen already that the library version of `swap` makes unnecessary copies of the `string`s, managed by `HasPtr`. 
We could therefore write a `swap` function for `Foo`. 
If we: 
```
void swap(Foo& lhs, Foo& rhs)
{ 
	// WRONG ; this function uses the lib version of swap
	// NOT the HasPtr version
	std::swap(lhs.h, rhs.h);
	// swap other members of type Foo
}
```

The problem here is that we have requested the lib version of `swap`. 
We don't want that one, we want the one that we defined for `HasPtr` objects. 

The right way to write this is: 
```
void swap(Foo& lhs, Foo& rhs)
{ 
	using std::swap; 
	swap(lhs.h, rhs.h); // uses the HasPtr version of swap
	// swap other members of Foo
}
```
Each call should be to `swap` not to `std::swap`. 
It will be further explained later on, if there is a type-specific version of `swap`, that version will be a better match than the one defined in `std`. 
As a result, if there is a type-specific version of `swap`, calls to `swap` will match that type-specific version. 
If there is no type-specific version, then - assuming there is a `using` declaration for `swape` in scope - calls to `swap` will use the version in `std`. 
The idea is that we bring `std::swap` into scope, however, if there is a better preference, then we will use that one instead. 


Here is another example of how that might work: 
```
namespace A{ 
	void foo(){ 
		cout << "A::foo" << endl;
	}
}

namespace B{ 
	void foo() { 
		cout << "B::foo"
	}

	void bar() { 
		foo(); // calls B::foo
	}
}

// Remember how are calls work, look for the closes matching function first. 
// Look in the current scope first

int main(){ 
	using A::foo; 
	B::bar(); // calls B::foo even thought A::foo is brought into scope
}
```
However, if you use a `using` directive or declaration to bring a function into scope, it doesn't override the local definition of the function. Instead, it allows you to refer to that function without qualifying it with its namespace. But if there's a function with the same name in the local scope, it will take precedence over the one brought in by the `using` directive or declaration.
Apparently it will be explained even more later on too. 

I have just realised as well, when we do something like this: 
```
void swap(Test& lhs, Test& lhs)
{ 
	using std::swap;
	swap(lhs.name, rhs.name); // where name is arbitrary
}
```
We have to say using `std::swap` here otherwise, we will just call the same swap function recursively over and over. 

We might have: 
```
class Test{ 
public: 
	friend void swap(Test&, Test&);
};
```
The idea is that using `std::swap` is great for user defined functions. 

#### Using `swap` in Assignment Operators
Classes that define `swap` often use it to define their assignment operator. 
This is know as the `copy and swap`. 
Swapping the left hand operand, with a `copy` of the right hand operand
```
// note rhs is passed by value, meaning that HasPtr has a Copy Constructor
// copies the string in the right hand operand into rhs

HasPtr& HasPtr::operator=(HasPtr rhs)
{ 
	// swap the contents of the left hand operand with the local variable rhs
	swap(*this, rhs); // rhs now points to the memory this object had used
	return *this;
}
```

Here the parameter is not a reference, but by value. 
`rhs` is a copy of the right hand operand. 
Copying a `HasPtr` allocates a new copy of that object's `string`. 
Therefore, after `swap`, the pointer member in `*this` will point to the newly allocated `string` that is a copy of the right hand operand. 
When this is done, `rhs` is destroyed and the `HasPtr` destructor is run. 
Deleting the memory to what `rhs` now points, after the swap. 
This will automatically handle self assignment

The best thing about this, is that it is exception safe and handles self assignment. 
The only thing that throw an exception is the `new` expression inside the copy constructor. 

With swap, we have something cool called ADL, Argument-Dependent Lookup. 
Allowing functions to be found via the namespaces of their arguments. 
When we call a function with the arguments of a user-defined type, the compiler searches for that function, not only in the current scope, but also in the namespaces associated with the types of the function's arguments. 
In order to access this ADL idea: 
```
MyClass{ 
private: 
	int value;

public: 
	// obvious other elements here

	friend void swap(MyClass& a, MyClass& b)
	{
	using std::swap;
	swap(a.value, b.value);
	}
	
};
```

This will allow us to use ADL. 
So we are not making `std::swap` a friend function here, we are creating a custom function inside the namespace `MyClass`. 
Therefore, I write `using std::swap;` in order to make sure we are not calling recursively. The likelihood is small, as calling with other types will already change which `swap` that we will call anyways, but it is good practice. 

So here we are overloading the `std::swap` function, by putting it into the class namespace, objects that we are going to pass into `swap`. 
Therefore we can do something like: 
```
int main(){ 
	MyClass a(10); MyClass b(20);
	swap(a, b); // through ADL, this will find the user defined swap within MyClass
	// Therefore a.value will be 20, and b.value will be 10
}
```

#ADL   
#argumentdependentlookup
ADL is also known as Koenig Lookup, is a mechanism in C++ that extends the normal scope-based name lookup rules to include namespaces associate the function arguments. 
SOOOOOO when we pass in our classes and our structs, that means that our namespaces/classes etc. will be searched for that function as well. 
Even though they are not in the immediate scope where the function is called. 

So usually, the compiler will look for it in the current scope and its enclosing namespaces. 

Then with ADL we extend that search to include the namespaces associated with those argument types, so our types that we pass into arguments is really important. 

```
namespace MyNamespace{ 
	class MyClass {};
	void myFunction(MyClass obj){ 
		// some code
	}
}

int main(){ 
	MyNamespace::MyClass obj;
	// creating an obj of MyClass from MyNamespace
	MyNamespace::myFunction(obj); // ADL will search for
	// myFunction in MyNamespace
}
```
The most common uses for that involve `operator+` and `operator-`. 
Essentially, we are extending the scope-based name look up rules, to include namespaces associated with function arguments. 
This is something that we have been using this entire time, however, probably didn't know the name of. 

