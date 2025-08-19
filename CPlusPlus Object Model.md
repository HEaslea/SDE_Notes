
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

When we use `+`, we are going to use a temporary in order to store the value of the operator. 

If we were to do something like this: 
```
Warehouse small;
Warehouse mid;

// .. some data inserted into the samell and mid objects
Warhouse large{small + mid}; // here we are using the operator+
```

```
Warehouse operator+(const Warehouse& a, const Warehouse& b)
{ 
	Warehouse sum; // tmp
	sum.size_ = a.size_ + b.size_;
	sum.products_ = new Product[sum.capacity_];
	for(int ix = 0; ix < a.size_; ++ix)
	{ 
		sum.products_[ix] = a.products_[ix]; // just a copy at this point
	}
	for(int ix = 0; ix < b.size_; ++ix)
	{ 
		sum.products_[a.size_ + ix] = b.products_[ix]; // just copy after where a:ix is
	}
	return sum; // you can see that sum is a temp object
	// It may be RNO'd into the right spot, however, the temporary aspect of it is quite obvious
}
```

It's equivalent to:

```
Warehouse small;
Warehouse mid;
Warehouse tmp{operator+(small, mid)};

Warhouse large; 
Warehouse_copy_constructor(large, tmp);
__destroy_temporary(tmp); // if you were to imagine this
```

**Move Semantics**, in C++11, where we skip this temporary by moving the return value into the `Warehouse` object. 

To do so, we declare the move constructor for `Warehouse`, 
```
class Warehouse{ 
public: 
	Warehouse();  // default ctor
	Warehouse(const Warehouse&); // Copy Constructor
	Warehouse(Warehouse&&); // Move Ctor -> && signifies temp
};
```
Remember that `&&` is an rvalue reference. 

### LValue References
When a variable can be addresses and pointed to and has a scoped storage duration. 

```
double pi{3.14}; // lvalue
int x{42}; // lvalue
int y{x}; // lvalue
int& ref{x}; // lvalue-reference
```

References are really just `const` pointers: 

We should be thinking of references as a way to optimized lvalues. 

### Rvalue References 
As you know, we can't bind lvalue references to temporaries, as the data will be gone and the ref will point to an empty address. 
```
int get_it() { 
	int it{42};
	return it;
}

int& impossible{get_it()}; // oops
```

However, this would work: 
`int&& possible{get_it()};`

`rvalue` references allow us to skip a generation of temporaries as much as possible. 
We eliminate temporary objects. 

```
void do_something(int&& val)
{ 
	// do something with the val
}

// the return value of the get_it is moved to do_something 
// rather than copied 
do_something(get_it());
```

Just consider the effect of moving. 
```
int val; 

void get_it() 
{ 
	val = 42; 
}

void do_something()
{ 
	// do somethign with the val
}

do_something(); 
```

Before moving was introduced, the code before would look like this (with some compiler optimization). 

```
int tmp; 

void get_it()
{ 
	tmp = 42;
}

void do_something(int val) 
{ 
	// do something with this val
}

do_something(tmp);
```

We have the move ctor, and the move `operator=()`, has the effect of copying without actually carrying out a copy operation, when the input argument represents `rvalue`. 

```
class Warehouse { 
public: 
	// Ctors omitted
	Warehouse(Warehouse&& src)
		: size_{src.size_}, capacity_(src.capacity_), 
			products_{src.products_}
	{ 
		src.size_     = 0;       // leave src in a valid state
		src.capacity_ = 0; 
		src.products_ = nullptr; // as we have already "copied" over the pointer
		// in the memberwise copy area. 
		
	}
};
```
The main thing is that we are not creating a new array of size `capacity_`. 
Therefore we are not doing a deepcopy of all of the elements that are in the array. 

The idea of an `rvalue` means that that object is going to be useless in a little bit, and we can move everything from it. 

Remember that after all this, we get to the dtor. 

If we were still pointing to the array, then in the dtor, we would be deleting all the stuff in the array, therefore, we have to make sure that the pointer becomes `nullptr`. 

Now we know that when we do `Warehouse large = small + mid;` 
![[Pasted image 20250811114034.png]]

### Overloading Operators 
More explicit and, when done correctly, becomes an interface that is obvious to the use. 

`constexpr` used here, meaning that we can get the value at compile time, however it's not necessary, and can be done at run time. 

```
constexpr bool operator<(const Money& a, const Money& b)
{ 
	return a.value_ < b.value_; // defining which part of the object to compare
}
```

We can do much more than that. 
eg> 
```
constexpr Money operator-(const Money& a, const Money& b);
```
They should all be considered as friends and declared ass such in the `Money` class. 

In ++20, there is also the spaceship operator, which will allows the skipping of writing comparison operators: 
`operator<=>()`, aka the three way comparison, requests the compiler to generate relational operators.

```
class Money { 
	// most things omitted for brevity
	friend auto operator<=> (const Money&, const Money&) = default;
};
```

The compiler will now generate the ==, !=, <, >, <=, >= operators. 
The `<=>` operator doesn't just say "less/greater/equal", it will return a comparison category, encoding how strong your notion of comparison is, which sounds insane. 

`std::strong_ordering` - [CppReference](https://en.cppreference.com/w/cpp/utility/compare/strong_ordering) - it's a `class`, can do all six of `==, !=, <, <=, >, >=` and is engaged with when equivalent values are indistinguishable. It argues that the values would be substituted for one another. 

`std::weak_ordering` - [CppReference](https://en.cppreference.com/w/cpp/utility/compare/weak_ordering.html) - does not imply substitutability, two values can be equivalent, even if they are not identical. 
`'a' == 'A'`, can be equivalent, but are not identical. 

`std::partial_ordering` - [CppReference](https://en.cppreference.com/w/cpp/utility/compare/partial_ordering.html) and will admit incomparable values. There is the chance that for some x , neither `x < y`, `x == y`, nor `x > y`, truely unordered

The idea being we put that at the beginning of the `<=>` operator. 

```
std::strong_ordering operator<=>(const POint& rhs) const 
{ 
	if(auto c = x <=> rhs.x; c != 0) return c;
	return y <=> rhs.y;
}
```

### Class Relationships
How objects intercommunicate is the core part of software engineering, and OO systems. 

This relationship is called aggregation, `Warehouse` contains `Products`, 
![[Pasted image 20250811125456.png]]

There are, of course, a few relationships that are necessary to know about, 
- Association 
- Aggregation 
- Composition
- Instantiation
- Generalization

##### Aggregation and Composition
**Association** - showing that two classes are connected, so their objects communicate or reference each other at runtime. 

```
class Student { ... };

class Teacher { 
	Student* student; // association : Teacher knows of Student
public: 
	Teacher(Student& s) : student(s) {}
}
```

Python is a little more obtuse about this
```
class Student: 
	pass
	
class Teacher: 
	def __init__(self, student): 
		self._student = student # composition, which is still considered association
```
In UML, composition and aggregation are both special kinds of association, differing in ownership/lifetime semantics

**Aggregation** : class contains an instance or instances of other classes, that can be instantiated without the aggregate. 
This means that we can have a `Warehouse` without necessarily having the `Product` objects. 
This means that there is no ownership, in that the `Product` can exist without `Warehouse`

```
// Aggregation : School uses existing Teacher object (no ownership)

class Teacher { ... }:

class School { 
	// Aggregation : no ownership
	Teacher* teacher; // Pointer/reference to externally managed teacher 
public: 
	School(Teacher* t) : teacher(t) {}
};

Class Engine { ... };

class Car { 
	Engine engine; // the lifetime is tied to the Car;
public: 
	Car()
};


# Python doing python thing
class Teacher : pass 
class School
	def __init__(self, teacher):
		self.teacher = teacher # no ownership implied, as this could be a reference

# Composition
class Engine : pass
class Car: 
	def __init__(self): 
		self.engine = Engine() # created and owned here
```
Remember that aggregation means, the formation of a number of things into a cluster.
These things have their own lifetimes that are no tied to this object that they are in. 

Another example of aggregation, which is very clear, is that of a `Person` and a `Car`. 

Clearly a `Person` is its own entity. 

```
class Person; // forward declaration
class Engine { ... };

class Car { 
	Person * driver_; // aggregation
	std::vector<Person*> passengers_; // aggregation as the lifetime of the person is not tied to the Car
	Engine engine_; // composition, the lifetime is tied
	
public: 
	Car(); 
	// ... 	
};
```

![[Pasted image 20250811132934.png]]

The blocked out array means that it is composition. 

**has-a** relationship. 
Aggregation - can have a. 
A car can have a drive. 

#### Inheritance Under the Hood
The four principles: 
- Abstraction
- Encapsulation
- Inheritance
- Polymorphism

##### Inheritance
The idea that we are reusing classes. The relationship here is `is-a`. 
`Car` is a `Vehicle`. 

```
clas Vehicle { 
	publice: 
		void move();
};

class Car : public Vehicle { 
public: 
	Car();
};
```

Generalization is the idea that the arrow is pointing from the more specialized class to the more generalized class -> 
Representing a `is-a` relationship. 
Meaning that the child class will be more specialized. 

The book says this not me, so w can take this as gospel lol : Only in extreme cases should you consider using inheritance. 

Therefore, we don't use it. 
Classes should satisfy the is-a relationship, as we noted previously, although this might occasionally be tricky. 

Imagine that we have a `Square` that `is-a` `Rectangle` therefore we can have inheritance here. 

Then we would need to override the `area()` method and implementation. 

As `Square` is a `Rectangle`, it should be used anywhere `Rectangle` is used, 

```
void make_big_rectangle(Rectangle& ref)
{ 
	ref->set_width(870);
	ref->set_height(940);
}
```

The Liskov Substitution Principle is an instance of a successful type substitution with its subtype (contra and co variance). 

### Inheritance from the Compiler Perspective

![[Pasted image 20250812230734.png]]

We have `Rectangle&`, behaving similar to a pointer. 
Pointers are 4 bytes on a 32 bit system, and of course 8 bytes from 64 bit system. 

Wherever we use a reference, we are referencing to whatever was passed in, from wherever it was passed in, therefore, we have to keep that object alive until we have gone back out of scope, and the temporary object, that we are referencing from. 
![[Pasted image 20250812231425.png]]

Note that in `Square` there is a `Rectangle` subobject. 

Here the `Square` object doesn't add any additional data members to the rectangle. 

The type of the pointer specifies how many bytes should be read from the pointer's starting location. 

`ref` stores a copy of the starting address of the local `rect` object declared in `main()`. 

When `make_big_rectangle()` accesses the member functions via `ref` eg. `ref.some_member_function()`, it actually calls the global functions that take a `Rectangle` reference as their first parameter. 


Remember, when we write a non-static member, the compiler will transform it into a regular function, that takes an extra hidden parameter - a pointer to the instance of the class `this`. 

Static member functions are don't take a `this` pointer, and are basically normal functions in the class' namespace. 

Say we have: 
```
void make_big_rectangle(Rectangle * const ref)
{ 
	Rectangle_set_width(*ref, 870); // this was a member function call
	Rectangle_set_height(*ref, 940); 
}
```

Remembering that this is a reference to a `Rectangle` therefore if we pass in a `Square` , that means that we will only have access to the `Rectangle` part of the `Square`. 
We are passing the starting point of the `Square` object, and because it has a `Rectangle` sub-object, we can pass it to the `Rectangle` reference argumented function. 

However, we only have access to the `sizeof(Rectangle)` bytes of the object, which won't see the additional bytes of the actual `Square` object. 

![[Pasted image 20250812233215.png]]

Inheriting `Square` from `Rectangle` is almost the same as declaring two structs, one of which contains the other. 

```
struct Rectangle { 
	int width_;
	int height_;
};

void Rectangle_set_width(Rectangel& this, int w)
{ 
	this.width_ = w;
}

void Rectangle_set_height(Rectangle& this, int h)
{ 
	this.height_ = h;
}

int Rectangle_area(const Rectangle& this) 
{ 
	return this.width_ * this.height_;
}

struct Square 
{ 
	Rectangle _parent_subobject;
	int area_;
}

void Square_set_side(Square& this, int side) 
{ 
	// Rectangle_set_width(static_cast<Rectangle&>(this), side);
	Rectangle_set_width(this._parent_suboject_, side);
	
	// Rectangle_set_height(static_cast<Rectangle&>(this), side);

	Rectangle_set_height(this._parent_subobject_, side);
}

int Square_area(Square& this)
{ 
	// this.area_ = Rectangle_area (static_cast<Rectangle&>(this));

	this.area_ = Rectangle_areaw(this._parent_subobject_);
	return this.area_;
}
```

It's clear that `Square` has a `Rectangle` object here. 

Then we are static casting the `this` to `Rectangle&`. so that we can pass it to the right member functions. 

### Composition Vs Inheritance
The compiler treats the inheritance relationship more like a composition. 
It is rather preferable to use composition over inheritance wherever we can. 

One of the reasons that the inheritance of `Square` and `Rectangle` was a poor design decision : The Liskov Substitution Principle, which allows us to use `Square` incorrectly by passing it to a function that transforms it into a `Rectangle`, which means we are not using it as a `Square`, which seems obvious, but we are breaking the way that the object is invariantly supposed to work. 

This will reveal that the `Square` is no `Rectangle` at all, they do not behave in the same way at all. 
The `is-a` connection is **not correct**, in this sense of behaviour. 

It is an adaption of `Rectangle`, not an `is-a` connection at all. 

The idea mainly being that setting width and height of square separately should never be a thing, however, it is a thing with rectangle. 

However, what if we were to privately inherit from `Rectangle`. 

Inheriting privately gives a sense of composition: 
```
class Square : private Rectangle { 
public: 
	void set_side(int side);
	// Rectangle's public interface is accessible to the Square
	set_width(side);
	set_height(side);
	{ 
		int area() 
		{ 
			area_ = Rectangle::area();
			return area_;
		}
	}
private: 
	int area_;
};
```

This means that the client cannot access the members that were inherited, however, `Square` can. 
However, if there were another class that inherited from `Square` that would not be able to access those inherited from `Rectangle`. 

```
Square sq; 
sq.set_width(14); // Compile Error , Square has no such public member


make_big_rectangle(sq); // Compile error, can't cast Square to Rectangle
```

And, we can achieve the same by declaring a `Rectangle` member as private in `Square`: 
```
class Square { 
public: 
	void set_side(int side)
	{ 
		rectangle_.set_width(side);
		rectangle_.set_height(side);
	}

	int area() { 
		area_ = rectangle_.area();
		return area_;
	}

private: 
	Rectangle rectangle_;
	int area_;
};
```

Given the option, composition is just a healthier pick, however, there are times that we use inheritance. 

The default access modifier for classes is private, so `class Square : private Rectangle {};`  si the same as 
`class Square : Rectangle {};`


### Protected Inheritance
Users won't be able to access protected members, but derived classes can. 
Similarly when we inherit through protection, public interfaces become only usable to the deriving classes. 
Not with `private` where the public becomes only usable to the top level driving classes. 

imagine that we are implementing a stack data structure, over a deque, linked list, or a vector. 

A `stack` does not represent a data structure, but rather how one is used. 

We might have something like this: 
```
class Stack : protected Vetor { 
	 // ... 
};

class AdvancedStack : public Stack { 
	// ... 
};
```

Here, we are taking in the vector member functions through the protected filter, meaning that both the stack classes can use the, however, the users will not be able to use them through a `Stack` or `AdvancedStack` instance. 

### Polymorphism
Subclasses implement the methods that are descended from the base class in their own unique ways. 
```
class Musician { 
public: 
	void play() { std::cout << "Play an instrument"; }
};

class Guitarist { 
public: 
	void play_guitar() { std::cout << "Play a guitar"; }
};
```

Here, it's obvious that a `Guitarist` is a `Musician`. 

Just making use of `virtual` functions for dynamic dispatch. 

```
class Musician { 
public: 
	virtual void play() { std::cout << "Play an instrument"; }
};

class Guitarist : public Musician { 
public:
	void play() override { std::cout << "Play a guitar"; 
};
```

Then it's really clear when we move into this idea: 
```
Musician armstrong; 
Guitarist steve;

Musician* m = &armstrong; 
m-> play(); // 

m = &steve; 

m->play();
```

Remember pure virtual functions: `virtual void play() = 0;`

Then we cannot create an actual instance of `Musician`, as we have an `undefined` function. 

**Abstract Classes** : are designed to be inherited from, however, can also implement some of the functions, making it halfway between a class and an interface, meaning there can be functions with and without implementations. 

```
class Pianist : public Musician { 
public: 
	void play() override { std::cout << "Play a piano"; }
};
```

Then if we have a function declared, that returns a collection of musicians, either `Guitarist`s or `Pianist`s, to demonstrate the full potential of polymorphism. 

`std::vector<Musician*> get_musicians();`

Then each musician will dynamically dispatch to the correct function call. 
```
auto all_musicians = get_musicians(); 
for(const auto& m : all_musicians)
{ 
	m->play();
}
```

### Virtual Functions Under the Hood
Virtual functions are a runtime polymorphism, there are others that utilise templates. 
They are the most important and most used form of polymorphism. 

As soon as we have a virtual function, the compiler will add an additional pointer to the class. 
The pointer points to a table, that's referred to as a virtual functions table, or simply `virtual table`. 
Therefore the pointer was `virtual table pointer`. 

Say that we need a cashing out system for a bank that is determined by the type of account.

```
class Account 
{ 
public: 
	virtual void cash_out() { 
		// the default implementation for cashing out
	}
	virtual ~Account() {}; // remember that this is always necessary

private: 
	double balance_;
}
```

The compiler will give this a pointer to the virtual functions table. 

This pseudocode gives us an idea of what is going on. 
There is the idea of name mangling too. 

```
struct Account 
{ 
	VTable* __vptr;
	double balance_;
};

void Account_constructor(Account* this)
{ 
	this->__vptr = &Account_VTable;
}

void Account_cash_out(Account* this)
{ 
	// the default implementation for cashing out
}

void Account_destructor(Account* this) {}
```

See the added `vptr`  member here, and then we assign it in the constructor. 

Think of the table as an array of pointers to those functions: 
```
VTable Account_VTable[] = 
{ 
	&Account_cash_out;
	&Account_destructor;
}
```

Then the compiler will produce this sort of code, when we call a virtual function. 
```
// consider the get_account() function as already 
// implmented and returning an Account*
Account* ptr = get_account(); 
ptr->cash_out();
```
```
Account* ptr = get_account();
ptr->__vptr[0](); // then call the function
```

Say that we have a hierarchy: 
```
class SavingsAccount : public Account
{
public: 
	void cash_out() override { 
		/// ... 
	}
	
	virtual ~SavingsAccount() {}
};
```

When calling `cash_out` through a pointer or a reference (either work just fine for polymorphism at run time), the virtual function is invoked based on the target object that the pointer points to

If there is no overriding, remember that `override` should be used in order to provide better debugging, then using `cash_out()`, just places the address of the base class implementation in the same slot as `SavingsAccount_VTable`, shown here: 
```
VTable* SavingsAccount_VTable[] = { 
	// the slot contains the base class version
	// if the dervied class doesn't have an implementation
	&Account_cash_out;
	&SavingsAccount_destructor
}
```

### Classic Design Patterns
You know the drill, Gang of Four all of that. 

The general: 
- The pattern name
- The problem
- The solution
- The consequences

Having a name, instantly makes something recognizable, instantly tells us the problem, instantly gives us an idea of the code that we are looking at. 
Having a pattern that is recognizable, makes all of this easier. 

- Intent
- Motivation
- Structure - class diagram illustrating the many components of the pattern and their relationships. 
- Participants - the classes that are used.
- Code Sample - give us a look

**Singleton** : is the most basic one to illustrate this. 
Defining just one instance. Imagine that this is a `Warehouse`. 
One and then everyone have access to it. 

```
class Warehouse { 
public: 
	statice Warehouse* create_instance() 
	{ 
		if(instance_ == nullptr)
		{ 
			instance_ = new Warehouse(); // using a pointer is the best way here
		}
		return instance_;
	}
	

	statice void remove_instance() 
	{ 
		delete instance_;
		instance_ = nullptr;
	}

private: 
	Warehouse() = default;
	inline static Warehouse* instance_ = nullptr; // didn't know member variables could be inline
};
```

Remember that `inline` allows multiple definitions across source files. 
`inline` changes the ODR (One Definition Rule), so that the same entity can be found in multiple translation units, as long as they are all identical, which given in a `.hpp` that will be fine. 

If we have a `static` data member, we can declare wherever we want, HOWEVER , we have to have the definition in one `.cpp` file. 

With `inline` the definition in the class is that definition. 
Every `.cpp` file that gets the header gets a copy. The linker will then merger them into a single entity. 

`inline` will mean that they will all merge, as long as they are all the same. 

`inline` AKA Merge, as long as they are the same. 

Here the client has to use the `create_instance()`, in order to access `Warehouse`. 

```
Warhouse* w = Warehouse::create_instance();  
Product book; 
w->add_product(book);
Warhouse::remove_instance(); 
```

There are three kinds of patterns: 
- Creational - Creating more complex situations, with adaptive structures etc.
- Structural - Object generation
- Behavioural - distributing responsibilities and algorithms, how these objects act and how to connect and join without coupling.

Here's one:

###### The Composite Pattern
Handling a collection of items, putting them into a single object, a composite pattern is used. 
This pattern will combine elements in terms of a tree structure. 
This is more of a structural pattern, as we are building a tree out of a collection of elements. 

Intent - Composing objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly. 

There is the odd idea here is that something can contain itself. 

A box can contain anything that fits in it, it can contain another box etc. 

Consider that we have to count all the books in the box. 
We know a box could contain a book or another box, or something else etc.
Open it, take out the book, and count them. 
However, this is not easy in code due to the many nesting levels. 

The idea is that of recursion, if there's another box, go into that as well. 

The common interface that we give an object is called a component. 

The main thing to think about is that for every object is there is a common interface. 

`Component` - common interface, the object that cannot contain itself. 

And the object that can contain itself is called the `Composite`. 

![[Pasted image 20250814172222.png]]
![[Pasted image 20250814173412.png]]

The dotted line with closed unfilled arrowhead means realization (or implements in code terms). 
`Leaf` and `Composite` both **realize**/**implement**, the `Component`interface/abstract class. 

`Component` defines the contract `method()` and both subclasses need to implement that function. 
It implies inheritance, however: not normal class inheritance (which is a solid line, with a hollow arrow head). 

**Normal Inheritance (solid line in UML)**
```
class Animal { 
public: 
	void eat() { std::cout << "Eating...\n"; }
};

class Dog : public Animal { 
public: 
	void bark() { std::cout << "Woof\n"; }
};
```
This is the solid white line. 

**Interface inheritance / realization (dotted line in UML)** 
This is where the parent is purely abstract - no concrete implementation (like an interface). 
```
class Component{ 
public: 
	virtual void method() = 0; // pure virtual - must override
	virtual ~Component() = default;
};

class Leaf : public Component { 
public: 
	void method() override { std::cout << "Leaf action\n"; }
};

class Composite : public Component { 
public: 
	void method() override { std::cout << "Composite Action\n"; }
};

int main() { 
	Component* c1 = new Leaf(); 
	Component* c2 = new Composite(); 


	c1 -> method(); // leaf action
	c2 -> method(); // composite action

	delete c1;
	delete c2;
}
```

The **component** defines the Composition's interface, can provide the default behaviour for the interface shared by all classes, defines a management and access interface for its child components. Defines an interface to gain access to the component's parent in a recursive structure. 

**Composite** describes how the components that have children should behave, storing components as children, implements any operations relating to children in the component interface. 

**Leaf** representing the composition's leaf object, obviously cannot have children of its own

In order to work with this, we have to work with the `Component`, and any requests are handled directly if they are a `leaf`. 

Take a task management system. 

The simple tasks, that will be `tasks`, while other tasks may have subtasks that will be called `projects`. 

**![[Pasted image 20250814182609.png]]**

Here we go: 
```
class ListOfTasks { 
public: 
	// ... 
	virtual std::string getTask() = 0;
};

class Task : public ListOfTasks { 
public: 
	// ...
	std::string getTask();
private: 
	std::string taskDescription;
};

class Project : public ListOfTasks { 
public: 
	// ... 
	std::string getTask();
private: 
	std::string projectName; 
	std::vectr<ListOfTasks*> tasks;
};
```
There are lots of things not written here. 

We know that `ListOfTasks` is the abstract class, hence the dotted line with the hollow arrow: 

```
class ListOfTasks { 
public: 
	virtual void setTask(std::string) = 0;
	virtual std::string getTask()const = 0;
	virtual bool isDone() const = 0; 
	virtual void setDeadline(std::string) = 0; 
	virtual std::string getDeadline() const = 0; 
};

class Task : public ListsOfTasks { 
	std::string m_task_description;
	std::string m_deadline;
	bool m_done;

public: 
	// value initialize m_task_description, 
	Task() : m_task_description{}, m_done(false) {}
	Tas(std::string task) : m_task_description(task) {}
	Task(std::string task, std::string deadline) : 
		m_task_description(task)
		{
			this->setDeadline(deadline);
		}
		
	void setTask(std::string task)
	{ 
		m_task_description = task;
	}

	std::string getTask() const 
	{ 
		return(m_task_description + "\t---\t" + this->getDeadline() + "\n");
	}

	void setDeadline(std::string deadline)
	{ 
		m_deadline = deadline;
	}

	std::string getDeadline() const 
	{ 
		return m_deadline;
	}

	void markDone()
	{ 
		m_done = true;
	}

	bool isDone() const 
	{
		return (m_done == true);
	}
};
```

Everything is treated as a `task`, also going inside the projects in order to find tasks there. 

###### The Decorator Pattern
This was so widely spread that the Python syntax just out right included it. 

Instead of modifying old functions, we just wrap them in new behaviours, really really simple. 

Obligations to a specific object rather than a class as a whole. 




// END
120 / 605 at the top
