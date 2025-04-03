#literalclass
Remember earlier that we said that the return types of a `constexpr` function would have to a  literal type. 
In addition to arithmetic types, references, pointers, certain classes can be literal types too. 
Classes that are literal types can also have `constexpr` function members as well. 

Such members must meet all the requirements of a `constexpr` function. 
These member functions are therefore implicitly `const`. 

An aggregate class, whose data members are all of a literal type is a literal class. 
A nonaggregate class, that meets the following restrictions, are also literal classes: 
- The data members all must have a literal type; 
- The class must have at least one `constexpr` constructor
- If a data member has an in-class intializer, the initializer for a member of a built-in type must be a constant expression, or if the member has class type, the initializer must use the member's own `constexpr` constructor. 
- The class must use default definition for its destructor, which is the member that destroys objects of the class type. 


#### `constexpr` Constructors
The idea is that we can create objects at compile-time if all the parameters are compile-time constants. 
Although constructors cannot be `const`, constructors in a literal class can be `constexpr` functions. 
Remember the requirements that a literal class must provide at least one `constexpr` constructor. 
A `constexpr` constructor can be declared as `= default`. Otherwise, a `constexpr` constructor must meet the requirements of a constructor - meaning it can have no `return` statement - and of a `constexpr` function - meaning the only executable statement it can have is a `return` statement. 
Therefore, the body of a `constexpr` constructor is typically empty. 

```
class Debug{ 
public: 
	constexpr Debug(bool b = true): hw(b), io(b), other(b){}
	constexpr Debug(bool h, bool i, bool o) : hw(h), io(i), other(o) {}
	constexpr bool any() { return hw || io || other;} // || is OR operator
	void set_io(bool b) { io = b; }
	void set_hw(bool b) { hw = b; }
	void set_other(bool b) { other = b; }


private: 
	bool hw; // hardware errors other than IO errors
	bool io; // IO errors
	bool other; // other errors
};
```
A `constexpr` constructor must initialize every data member. 
The initializers must either use a `constexpr` constructor or be a constant expression. 

A `constexpr` constructor is used to generate objects that are `constexpr` and for parameters or return types in `constexpr` functions. 
```
constexpr Debug io_sub(false, true, false); // debugging IO, object creation here
if(io_sub.any()); // equivalent to if(true)
	cerr << "Print appropriate error messages" << endl;
constexpr Debug prod(false); // no debugging during production
if(prod.any()) // equivalent to if(false)
	cerr << "Print an error message" << endl;
```