Constructors are not inherited in the normal sense of the word. 
As we have seen, we can use the constructors of the direct base class. 
A class will only inherit constructors from its direct base. 
A class cannot and will not inherit the default, copy, and move constructors. 

If the derived class doesn't directly define these, then the compiler will synthesize them for us. 

A derived class inherits its base-class constructors by providing a `using` declaration that names its (direct) base class. 
Here's how `Bulk_quote` can inherit `Disc_quote` constructors. 

```
class Bulk_quote : public Disc_quote{ 
public: 
	using Disc_quote::Disc_quote; 
	// inherits Disc_quote's constructor
	double net_price(std::size_t) const;
};
```
Usually a `using` will only make the name visible within current scope, (also remember that derived classes are nested scopes). 
But here, when we write `using` for a constructor, then we are actually generating some code. 
The compiler generates a derived constructor corresponding to each constructor in the base. 

For each constructor in the base class, the compiler generates a constructor in the derived class that has the parameter list. 
These compiler-generated constructors have the form

`derived(parms) : base(args) {}`

`derived` is the name of the derived class, `base` is the name of the base class, `parms` is the parameter list of the constructor and `args` pass the parameters from the derived constructor to the base constructor. 

In our `Bulk_quote` class, the inherited constructor would be equivalent to
```
	Bulk_quote(const std::string& book, double price, std::size_t qty, double disc) : Disc_quote(book, price, qty, disc) {}
```
Notice that it doesn't define any of the derived data members. 
It will just call the direct base classes constructor with the args that that class gives to our derived class. 

Remember that using can change the access level of the members that it is eluding to. 

However, when we do this with constructors, the access level carries over. 

Our using can be anywhere, under any access modifier, if the constructor that it is `using` was a `private` constructor in the direct base class, it will be a `private` constructor in the derived; similar with `public, protected`. 

This is true too of `explicit` and `constexpr`, if it was either of those (or both) in the base, it will be the same in the derived. 
`using` here carries a few things with it: access level, `explicit` use, and `constexpr` use. 

There is also this idea: 
If the base class has arguments/parameters that are defaulted, then we actually will inherit multiple constructors. The "first" will have a constructor that takes all the arguments, then we create others that successively get rid of defaulted arguments. 
eg. if the direct base class has two args, one is defaulted, then we will inherit two base classes: one with both parameters (and no default argument) and the other with a simple parameter corresponding to the left-most, non-defaulted parameter in the base class. 

If the base class has several constructors, then with two exceptions, then the derived class will inherit each of them. 
The derived class can inherit some constructors and define its own versions of other constructors. If a derived class defines a constructor with the same parameters as the constructor in the base, then the constructor is not inherited. The one defined in the derived class is used instead of the inherited constructor. 

Default, move, and copy constructors are not inherited. They are synthesized if not defined. 
An inherited constructor is not treated as a user-defined constructor. 
Therefore, a class that contains only inherited constructors will have a synthesized default constructor. 

The general idea is that using `using` here will inherit all the constructors, and will define them in turn depending on default arguments. 
If we wanted to only use some of the direct base class constructors, then we have to call them. 

so here are two ways of doing a thing:): 
```
class A { 
public: 
	explicit A(int x) {}
};

class B : public A{ 
	using A::A; // using using to inherit A's constructors
};
```

Here's another rule as well: 
```
struct Base{ 
	Base(int) {} // this should be explicit
};
```
This should be explicit, as for any constructor that only takes on arg/type parameter. Here an int can be "converted" or constructed into a Base type . 
We don't want that functionality just casually roaming around. Therefore, we should explicit it as much as we can. 

We can also do this to call specific constructors from the base class: 
```
class A{ 
public: 
	explicit A(int x) {}
}; 

class B : public A{ 
public: 
	explicit B(int x) : A(x) {}
};
```

This way is very familiar to python, it's almost the exact same. 

```
struct B1{ 
	B1(int) {}
};

struct D1 : B1 { 
	using B1::B1; // implicitly declares D1(int)
	int x;
};

void test()
{ 
	D1 d(6); // d.x will not be initialized here
	D1 e; // D1 has no default constructor
}
```
