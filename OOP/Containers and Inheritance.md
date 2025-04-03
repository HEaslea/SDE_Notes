#containersandinheritance
Generally, when we use a container to store object from an inheritance hierarchy, we must generally store those objects indirectly. 

We cannot really put objects of types related by inheritance directly into a container, because there is no way to define a container that holds elements of differing types.

Can't have a vector of `Base` and `Derived`. 

We also, weirdly can't add objects of the base class either. Say if we had a vector of type `Quote`, we can put objects of type `Bulk_quote` into the vector, as they are still `Quote` objects, just with the derived part too. 
However, the vector will cut off the `Bulk_quote` sub object portion. 

eg. 
```
vector<Quote> basket;
basket.push_back(Quote("0-201-82470-1", 50));

// ok , but only copies the Quote part of the object into the container
basket.push_back(Bulk_quote("0-201-54848-8", 50, 10, 2.5));

// calls version defined by Quote, prints 750, 
cout << basket.back().net_price(15) << endl;
```


The elements are purely `Quote` objects. 
Adding a `Bulk_quote` object to the `vector` its derived part is ignored. 

This is where we get object slicing. Containers and inheritance do not mix so well. 

#### Put (Smart) Pointers, not object, in Containers
Typically here we define the container to hold pointers (preferably smart pointers) to the base class, as I had thought.
Therefore we can write something like this: 
```
vector<shared_ptr<Quote>> basket;
basket.push_back(make_shared<Quote>("0-201-82470-1", 50));

basket.push_back(make_shared<Bulk_quote>(/*numbers*/));

// now when we call this: 
cout << basket.back()->net_price(15) << endl;
```
Here the call works on the dynamic typing, given that the member functions are virtual, otherwise it is all statically typed. 

Smart pointers will work the same way that the built-in pointers will work here, they can be used to up-cast and down-cast etc. 

It looks a little weird here, but playing around with this code, can be pretty useful: 

```
class Base {
public: 
	int value;
	Base() : value(0) { printf("Base Ctor default\n"); }
	Base(int a) : value(a) { printf("Base Ctor with int\n"); }

	Base(const Base& other) : value(other.value) { printf("Base Copy Ctor\n"); }

	virtual void PrintOut() {
		printf("Base Printout Called\n");
		cout << value << "\n\n";
	}

};

class Derived : public Base {
public: 
	string name; 
	Derived() : Base(), name("Default") { printf("Derived Ctor default\n"); }
	Derived(string n) : Base(), name(n) { printf("Derived Ctor with string\n"); }
	Derived(string n, int a) : Base(a), name(n) { printf("Derived Ctor with int and string\n"); }
	Derived(const Derived& other) : Base(other), name(other.name) { printf("Derived Copy Ctor/ using Base"); }
	// Derived(const Derived& other) : name(other.name) {}
	// Don't uncomment this unless you want derived object base subobject to be default 
	// constructed on copy of derived object.

	void PrintOut() override {
		printf("Derived Printout called\n");
		cout << name << "\n\n";
	}
};

int main(){ 
	vector <shared_ptr<Base>> vB; 
	vB.push_back(make_shared<Derived>("Hugo", 10));

	vB.back()->PrintOut();
```

#### Writing a `Basket` class
Whenever we write pointers in classes it's a good idea to write auxiliaries to maintain everything. 

```
class Basket{ 
public: 
	// Using synthesized default and copy-control members
	void add_item(const std::shared_ptr<Quote> &sale)
		{ items.insert(sale); }

	// prints the total price for each book and the overall total for all items in the basket

	double total_receipt(std::ostream&) const;
	
private: 
	// function to compare shared_ptrs needed by the multiset member
	static bool compare(const std::shared_ptr<Quote> &lhs, 
		const std::shared_ptr<Quote> &rhs)
		{ return lhs->isbn() < rhs->isbn(); }
	// multiset to hold multiple quotes, ordered by the compare member
	std::multiset<std::shared_ptr<Quote>, decltype(compare)*>
		items { compare };
};
```

Remember that when you create a map in C++, we have to pass in the type of pointer that we are using; 
`std::map<string, int, bool (*)(string, string)`. 
The elements in the multiset are `shared_ptr`s and there is no less-than operator for `shared_ptr`. 
We define a `private static` member here, named `compare`, to compare the `isbn`s. 
We in-class initialize the  multiset here. 

```
//multiset to hold multiple quotes, ordered by the compare number
std::multiset<std::shared_ptr<Quote>, decltype(compare)*>
	items { compare };
```
Remember that we have to initialize the multiset with the comparing function (`compare` in this case) in the braced initialize list. 

#### Defining the Members of Basket
We use an `add_item` taking a `shared_ptr` to a `Quote` and add it to the multiset, that is ordered by the `isbn` of those `shared_ptr`'s `Quote`'s `isbn`. 

`total_receipt` will print the itemized bill for the contents of the basket and returns the price for all the items in the basket: 
```
double Basket::total_receipt(osteam &os) const
{ 
	double sum = 0.0; // holding running total
	// iter refers to the first element in the multiset with 
	the same ISBN
	// upper-bound will return one past the last element
	for(auto iter = items.cbegin(); iter != items.cend(); 
		iter = items.upper_bound(*iter)) { 
			// we know there's at least one element with this key in the Basket
			// print the line item for this book
			sum += print_total(os, **iter, items.count(*iter));
			// we need the double dereferencing 
		}
		os << "Total Sale: " << sum << endl;// print the final overall total 
		return sum;
}
```
Here our `for` loop starts be defining and initializing `iter` to refer to the first element in the `multiset`. 
The condition will check whether `iter` is equal to `items.cend()`. 

The more interesting bit is the increment part of the list: 
Rather than use the usual loop that reads each element, we advance `iter` to refer to the next key. 
We have to skip over all the elements that match the current key by calling `upper_bound`. This call will return the iterator that refers to the element just pas the last one with the same key as in `iter`. The iterator we get back denotes either the end of the set of the next book. 

Inside the `for` loop, we call `print_total` to print the details for each book in the basket. 

`sum += print_total(os, **iter, items.count(*iter))`
Remember that when we dereference `iter` we get the `shared_ptr` pointing to the `Quote`. 

`print_total` will make a virtual call to `net_price`, the result will depend on the dynamic type of `**iter`. 
The `print_total` will print the given total of the book. 


##### Hiding the Pointers
Users of `Basket`, sadly, still have to use the idea that we are pointing to the `Quote`objects. We would have to write code like this: 
```
Basket bsk; 
bsk.add_item(make_shared<Quote>("123", 45));
bsk.add_item(make_shared<Bulk_quote>("345", 45, 3, .15));
```

It would therefore be easier for the user to take a `Quote` item directly. 
We do this in our implementation - whether there's a way of doing in this C++, and just referencing the Quote object itself, that just returns the pointer of it? 
Let's define two versions of it: 
```
void add_item(const Quote& sale): // copy
void add_item(Quote&& sale); // move
```
The only issue here is that we might have object slicing, if we send in a `Bulk_quote` and then have to use a `shared_ptr` to initialize that, then we might be using `new Quote(sale)`. 
If `sale` wants to refer to a `Bulk_quote` item, however, it will be sliced down here when we use `new Quote(sale)`. 

#### Simulating Virtual Copy
All of our training has been to use virtual functions here: 
We add to `Quote` class, a virtual member that allocates a copy of itself: 

```
class Quote{ 
public: 
	// virtual fucntion to return a dynamically allocated copy of itself
	// these mmebers use reference qualifiers! yay!
	virtual Quote* clone() const { return new Quote(*this);}
	virtual Quote* clone() && 
		{ return new Quote(std::move(*this));}
	// remember our move semantics from modern c++
};

class Bulk_quote : public Quote{ 
	Bulk_quote* clone() const override &
		{ return new Bulk_quote(*this); } // i believe this is the right ordering

	Bulk_quote& clone() && 
		{ return new Bulk_quote (std::move(*this)); }
	// other members as before
};
```

We are defining both rvalue and lvalues of `clone` here. 

Each one will allocate a new object of its own type. 

```
class Basket{ 
public: 
	void add_item(const Quote& sale) // copy the given object
		{ items.insert(std::shared_ptr<Quote>(sale.clone()));}

	void add_item(Quote&& sale) // move the given object
		{ items.insert(
			std::shared_ptr<Quote>(std::move(sale).clone()));}
	// other members as before
};
```

`clone` here will be overloaded depending on the item that it is called on, and lvalue or an rvaleu. 
The first version `add_item` calls the `const` lvalue version of `clone`, the second is an rvalue reference. 

The `clone` is also virtual. 
Whether the `Quote` or `Bulk_quote` version is run, depends (as usual) on the dynamic type of `sale`. 
Regardless of whether we are copying or moving. 

The idea being that we call the right virtual, allocate a new object of that type, return a pointer, use that pointer to create a `shared_ptr<TypeUsed>` and then use that to add items to the map/list. 

This means that we can bind a `shared_ptr<Quote>` to a `Bulk_quote*`. 

