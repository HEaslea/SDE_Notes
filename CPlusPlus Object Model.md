
Most of this will be some form of revision ; can't hurt to go through again, although sections may be skipped.

Given this: 
```
class Rectangle { 
	int m_length;
	int m_width;
public: 
	Rectangle (int length = 0, int width = 0) : 
		m_length(length), 
		m_width(width) {};

	int get_length() { return m_length; }
	int get_width() { return m_width; }

	void set_length(int l) { m_length = l; }
	void set_width(int l) { m_width = 1; }
	int perimeter()
	{ 
		return 2 * (m_length + m_width); 
	}
};
```

We'll walk through some of the ways of presenting this in memory. 

Imagine that we have the memory of the functions elsewhere, meaning, we don't have to have the functions for every single instance of an object. 
![[Pasted image 20250808014631.png]]

We might then move to having points to the `int`s as well. 

Imagine instead that these were pointers to much longer and more lengthy objects, like arrays. 

![[Pasted image 20250808014948.png]]
The two are not as space efficient for each instance as much as this. 

Like the idea that we have a pointer to an array somewhere in memory, we'll do that with functions. 

![[Pasted image 20250808015203.png]]

Thinking about an instance of an object, of a class. 

Think about objects in memory as there being base subobjects, then the non-static data members in declaration order (which can be very important, where we have padding for alignment). 

`static` will not be in the object/instance. 

There is the idea of EBO (empty base optimization) : where we optimize the empty bases away. 

The `obj.f(a, b)` is compiled roughly as: `f(&obj, a, b)` - object's address as a hidden first parameter, `this` pointer, which you have used instinctively, given that we understand that the compiler will handle the rest for us, for the way that we call functions already in a member function (Python: `self.someMemberFunction()`). 
This is static dispatch, meaning that the function called depends on the type of the pointer, not to the type that we are pointing to. 

Virtual functions : dynamic dispatch : virtual objects : polymorphic objects contains a vptr, the vptr points to a vtable is shared per dynamic type (class). 
We get the function pointer from that vtable. 

However, let's take a look at this. 

![[Pasted image 20250808020245.png]]

The issue we should immediately be thinking about is, although the instance will take up less memory itself, it will take more time to run, as we have to jump about with all the pointers. 

There are two types of data type, static and non-static. 

And the functions: static, non-static, virtual. 

Static data members : static, non-static and virtual functions are allocated outside the object of the class. 

There is a trickier aspect to virtual functions. 
Virtual functions are called at runtime, there is a need for the object to have something that has access to the exact virtual function. 
They are called virtual because the exact function to call isn't fixed ("concrete") at compile time - resolved virtually at runtime, based on the dynamic type. 
Objects, therefore, hold a pointer, ie a virtual pointer, pointing to a table, aka the virtual table, the fields of which hold the address of the virtual functions. 

The first slot of the virtual table is usually RTTI information, this is Run-Time Type Information, which is just compiler-generated metadata, that we query for the dynamic type at runtime. 
Possibly a pointer to `type_info` object (which we can enquire about with `typeinfo` library). 

Possibly the worst way to implement, but will go through most of what we have just talked about here: 

```
class Rectangle { 
public: 
	Rectangle(int length = 0, int width = 0) { 
	m_length = length;
	m_width = width;
	}

	static int get_length() { return m_length; }

	int get_width() { return m_width; }

	virtual void set_length(int length) { m_length = length; }
	virtual void set_width(int width) { m_width = width; }
	
	int perimeter()
	{ 
		return 2 * (m_length + m_width); 
	}
private: 
	static int m_length;
	int m_width;
};
```


![[Pasted image 20250808094117.png]]

The actual way these objects are represented are a mixture of the models above. 
### State
All the members of a `Product` instance - is known as the **state** of the object. 

The state is determined by the values of the member variables. 

State changes when we have new values for attributes. 

### Identity
Just the name of the object, even if they are the same, the identity will separate them. 

### Mimicking a Class
C structs cannot have member functions, so we just make functions that take in the struct: 

![[Pasted image 20250808095128.png]]

It's good to think that sometimes the compiler will turn a class into a struct with global functions, however, it's a bit old school. 

### Working With Classes
```
class Product { 
public: 
	Product() = default; // generate a default constructor
	Product(const Product&); // Copy Constructor
	Product(Product&&); // Move Constructor
	Product& operator=(const Product&) = default; // assignment copy operator
	Product& operator=(Product&&) = default; // Move assigment operator.
	// dtor not written, should be generated by the compiler

public:
	void set_name(const std::string&);
	std::string name() const; // will change nothing in the object
	void set_availability(bool);
	bool available() const;
	
private: 
	std::string name_;
	double price_;
	int rating_;
	bool available_;
};

std::ostream& operator<<(std::ostream&, const Product&);
std::istream& operator>>(std::istream&, Product&);
```

Remember that a non-`const` object can call a `const` member function - they can act as a `const` if they want to. 

However, we cannot have a `const` object, acting as a `non-const` object at any point. 

![[Pasted image 20250808100218.png]]
Unified Modelling Language - just a standardized way of illustrating classes and their relationships. 

The `+` means that it is a public member function. 

`-` therefore means private. 

#### From the Compiler's Perspective

The above is just going to be transformed into this: 
```
struct Product { 
	std::string name_;
	bool available_;
	double price_; 
	int rating_;
};

// we forced the compiler to generate the default constructor

void Product_construtor(Product&);
void Product_copy_constructor(Product& this, const Product&);
void Product_move_constructor(Product& this, Product&&);

Product& operator=(Product& this, const Product&);

Product& operator=(Product& this, Product&&);

void Product_set_name(const std::string&);

std::string Product_name(const Product& this);

void Product_set_availability(Product& this, bool b);

bool Product_availability(const Product& this);

std::ostream& operator<<(std::ostream&, const Product&);
std::istream& operator>>(std::istream&, Product&);
```

You see the the compiler will just generate the same code, that we introduced earlier as a way to mimic class behaviour using a simple struct. 

### Initialization and Destruction
This is similar to python where we have `__new__()` and `__init__()`. 
The idea is that we have the memory allocation and then the creation of the object in that memory. 

C++ will allocate memory, however the initialization needs to be done by the programmer, which is why we have the constructor in the first place. 

Remember that destructors will call in the opposite order to constructors and memory allocation, due to it being on the stack. 

### Copying Objects
There are two kinds of copying - **deep** and **shallow**. 
We use the copy constructor and the assignment operator, therefore we control the semantics of copying. 

```
Product p1; 
Product p2; 
p2.set_price(4.2);
p1 = p2; // is copying as p1 already exists
Product p3 = p2; // not copying, p3 just being initialized here. 

// The compiler will generate this code
Product p1;
Product p2;
Product_set_price(p2, 4.2);
operator=(p1, p2);
Product p3;
Product_copy_constructor(p3, p2);
```

They will generate a member-wise copy. 

We may need to adjust this, if that would leave an object invalid

Say if you have a class that has a pointer to another object. `Products* _products`, then the top level pointer would be copied and not the actual products copied in memory elsewhere. 
This is a fault of member-wise copy, in that it will be a shallow copy. 
Can be a logical error. 

We might rather a deep copy than a shallow copy. 

This might look like: 
```
class Warehouse { 
	// ... 
	Warehouse(const Warehouse& rhs)
	{ 
		size_ = rhs.size_;
		capacity_ = rhs.capacity_;
		products_ = new Product[capacity_];
		for(int ix = 0; ix < size; ++ix)
		{ 
			products_[ix] = rhs.products_[ix];
		}
	}
};
```

We need to create a whole new array. 

Then we need to copy them one by one. 

### Moving Objects
There are whole books written in perfect forwarding and move semantics. 

