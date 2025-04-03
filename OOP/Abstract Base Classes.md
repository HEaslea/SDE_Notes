A base class that is designed to be used only as a base class. 
Usually containing one or more pure virtual functions, making it incomplete and not suitable for instantiation on its own. 
Serving purely, as a template for derived classes to inherit from and implement their own versions of the pure virtual functions. 
```
class AbstractBase{ 
public: 
	virtual void pureVirtualFunction() = 0;
};
```
These cannot be instantiated - will result in compilation error. 

Any functions within these classes, might be used for overarching common functionalities for all derived classes, however, other than that, there isn't really anything that we need to think about. 

The idea from the book here is that we have a base class that is the "holder", the main base for all strategies of discount. 

But the issue here is that if we have `Disc_quote` without it own version of `net_price`, `Disc_quote` will inherit `net_price` from the Base `Quote`. 

However, we have to think about inheriting and late and early binding etc. etc. 
A user here could create an object of type `Disc_quote` by supplying a quantity and a discount rate. 
Passing the `Disc_quote` object to a function such as the `print_total` would would use the `Quote` version of `net_price`. The calculated price would not include the discount that was supplied when the object was created. Most of it doesn't make any sense lol. 

##### Pure Virtual Functions
In reality, we want people to not be able to make `Disc_quote` objects at all. 
The class will instead represent the general concept of a discounted book, not a concrete discount strategy. 

We can enforce this design intent - and make it clear that there is no meaning for `net_price` - by defining `net_price` as a **pure virtual** function. 

A pure virtual function **doesn't need to be defined**. 
We specify a pure virtual function, by using `=0` at the end. 

```
class Disc_quote: public Quote{ 
public: 
	Disc_quote() = default;
	Disc_quote(const string& book, double price, 
		std::size_t qty, double disc): 
			Quote(book, price), 
			quantity(qty), discount(disc) {}
	double net_price(size_t) const = 0;
	
protected: 
	size_t quantity = 0; // purchase size for the discount to apply
	double discount = 0.0; // fractional discount to apply
};
```
The ctors are needed here, for the derived classes from this class, to have that part of their object constructed. 

##### Classes with Pure Virtuals are Abstract Base Classes
A class with a pure virtual function is known as an abstract base class. 
Providing an interface that can be overridden by derived classes. 
`Disc_quote` defines `net_price` as pure virtual. 
We wouldn't be able to define objects of type `Disc_quote`. 

We can create objects that inherit from `Disc_quote`, they have to override `net_price`. 

```
// Disc_quote declares pure virtual functions, which Bulk_quote will override

Disc_quote discounted; // Erorr can't define this type fo object

Bulk_quote bulk; // This is fine as Bulk_quote has no pure virtual functions
```

If derived classes don't define `net_price` then they will be abstract as well. 


##### A Derived Class Constructor Initializes its Direct Base Class Only
```
// the discount kicks in when a spec'd number of copies of the same book are sold
// the discount is expressed as a fraction to use to reduce the normal price
class Bulk_quote : public Disc_quote { 
public: 
	Bulk_quote() = default;
	Bulk_quote(const string& book, double price, 
		size_t qt, double disc) : Disc_quote(book, price, qty, disc) {}

	// overrides the base version to implement the bulk purchase discount policy
	double net_price(size_t) const override; 
};
```
Direct base class: `Disc_quote`, Indirect Base Class : `Quote`. 

Now, each `Bulk_quote` object has three subobjects: 
1. An empty `Bulk_quote`
2. The `Disc_quote` subobject
3. The `Quote` subobject

We have to provide the same four args to this class, in order for it to pass up the hierarchy. 
This `Disc_quote` will pass some args up to `Quote`. 

Eventually, after `Quote` ctor, the `Disc_quote` will run. 
Then the `Bulk_quote` will resume. 

#refactoring
> **Refactoring**: 
> Adding `Disc_quote` to the `Quote` hierarchy is an example of **refactoring**. This is redesigning a class to move operations and/ or data from one class to another. Refactoring is common. 
> We would have to recompile after changing code like this. 


