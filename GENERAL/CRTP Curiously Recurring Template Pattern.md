Where a `Derived` class inherits from a template class `Base` that is parameterized with the `Derived` class itself. 
Allowing the base class to use methods and properties of the `Derived` class, achieving an odd form of static polymorphism. 
Static polymorphism means that the method to be executed is determined at compile time rather then at run time. 

```
#include <iostream> 

template <typename T> 
class Base{ 
public: 
	// Method that calls a method from the derived class
	void interface() { 
		static_cast<T*>(this)->implementation();
	}

	// Default implementation, which is optional
	void implementation(){ 
		std::cout << "Base Implementation" << std::endl;
	}
};
```

```
class Derived : public Base<Derived> { 
public: 
	// overriding the implementation method
	void implementation(){ 
		std::cout << "Derived Implementation" << std::endl;
	}
};
```

```
int main(){ 
	Derived d;
	d.interface(); // Outpouts : Derived Implementation

	return 0;
}
```


Another example: 
```
template <typename Derived> 
class Base
{ 
public: 
	Base() = default;

	void doSomething()
	{ 
		Derived& derived = static_cast<Derived&>(*this);
	}
};


class Derived : public Base<Derived>
{ 
public: 
	Derived() = default
};
```
The idea is that we are sort of "looking into the future" as to what is the derived type, that is being used in the template of the base class. Meaning that we can `static_cast`

We will know at compile time, what the derived type will be. 

Of course this means that we are optimizing virtual functions, where the type is dynamically arrived at. Determining what the type is, incurs some overhead performance issues. 

CRTP using compile-time resolution, eliminating the runtime overhead associated with dynamic dispatch. 

Errors will be caught at compile time, not at run time, which is nice. 
Function calls are resolved statically, there's no need for vtable lookups or runtime type checks, resulting in faster execution. 


Extending the Example: 

```
#include <iostream> 

// Template Base Class
template <typename T> 
class Base{ 
public: 
	// Method that calls a method from the derived class
	void interface(){ 
		static_cast<T*>(this)->implementation();
	}

	void foo(){ 
		std::cout << "Base Foo()\n";
	}

	void bar(){ 
		std::cout << "Base Bar()\n";
	}
};

class Derived : Base<Derived>{ 
public: 
	// Overriding the implementation Method; 
	void implementation() { 
		std::cout << "Derived Implementation\n";
	}

	void foo(){ 
		std::cout << "Derived Foo()\n";
	}

	void bar(){ 
		std::cout << "Derived Bar()\n";
	}
};

int main(){
	Derived d; 

	d.interface(); // Outputs: Derived Implementation
	d.foo(); // Outputs: Derived Foo()
	d.bar(); // Outputs: Derived Bar()

	Base<Derived> b;
	b.foo(); // Outputs: Base foo()
	b.bar(); // Outputs: Base bar()

	return 0;
}
```


#### Achieving Static Polymorphism
```
template <class Derived> 
struct base 
{
	void interface() 
	{ 
		// ...
		static_cast<Derived*>(this)->implementation();
		// ...
	}
};

struct derived : base<derived>
{ 
	void implementation()
	{ 
		// ... 
	}
};
```

Notice now, how `static_cast` makes so much more sense. 

### What is The Goal
The whole point of all of this is that we want to eliminate the use of virtual functions in order to achieve polymorphism. 
Of course this is crucial when we want to think about performance, as virtual functions require a look up table in order to call the right function.

Passing in the derived to the base. 
Policy designs require us to use template template parameters. 
In order to see polymorphic effects, we have to be able to call the derived class's (the one that we passed in) functions. 
In order to do that, we will have to actually access the derived class's functions, in order to do this we have to think about virtual functions actually do their thing: 

```
class Based
{ 
public: 
	virtual void outShout() 
	{ 
		cout << "BASED SHOUT" << endl;
	}
};

class Derived : public Based
{ 
public: 
	void outShout() override final
	{ 
		cout << "DERIVED SHOUT" << endl;
	}
};

int main() 
{ 
	Derived d;
	Base& bD = d;
	bD.outShout(); // DERIVED SHOUT 
}
```
Remember as well that we can use a variant in order to get a similar effect, each variant will have the same type, however, they will contain a different object, sort of like a union. 

