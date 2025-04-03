#classes 
#definingabstractdatatypes


Remember `Sales_item` class, that was an abstract data type, meaning that we have no access to the data members found within the `Sales_items`. 


However, the `Sales_data` class is not an abstract data type. 
Letting the users of the class access its data member and forces users to write their own operations. 
To make that class an abstract type, we need to define operations for users of `Sales_data`, once we have done this, we can hide its data members. 

### Defining `Sales_data` class
The `Sales_item` class had one member function, named `isbn` and supported the +, =, +=, <<, and >> operators. 

[[Member Function]]

The idea of the term "users" being used interchangeably, the person using the programme, or other coders writing and using classes. 

#### Using the Revised `Sales_data` Class
We would use functions to write a version of the bookstore program from earlier that works with `Sales_data` rather than `Sales_items`: 
```
Sales_data total;  // variable to hold the running sum
if(read(cin, total)) { // read the first transaction
	Sales_data trans; // var to hold data for the next transaction
	while(read(cin, trans)) { // read the remaining transactions
		if(total.isbn() == trans.isbn()) // check the isbns
			total.combine(trans); // update the running total
		else{ 
			print(cout, total) << endl; // print the results
			total = trans; // process the next book
		}
	}
	print(cout, total) << endl; // print the last transaction
}
else { // there is no input
	cerr << "No Data?!" << endl; // notify the user
}
```

#### Defining the Revised `Sales_data` Class
The idea is that we define: `bookNo`, a `string` representing the `ISBN`; `units_sold` and `unsigned` that says how many copies of the book were sold; and `revenue`, a `double` representing the total revenue for those sales. 

The class will have two member functions, `combine` and `isbn`. We'll also give it another function that we will call `avg_price`, it isn't intended for general use. It will be part of the implementation, not part of the interface. 

We define member functions similarly to ordinary functions. 
They MUST be declared inside the class. 
They <u>may be defined</u> outside the class body. 

Non-member functions that are part of the interface, such as `add, read, and print` are declared and defined outside the class. 

So here is the revised version of `Sales_data`. 

```
struct Sales_data{ 
	// new members: operations on Sales_data objects

	std::string isbn() const { return bookNo;}

	Sales_data& combine (const Sales_data&);

	double avg_price() const; 

	// data members are unchanged from earlier
	std::string bookNo;

	unsigned units_sold = 0; 

	double revenue = 0.0;
}
```

Non-member `Sales_data` interface functions
```
Sales_data add(const Sales_data&, const Sales_data&);
std::ostream

```

Functions defined in the class are implicitly `inline`. 


### Defining Member Functions
#definingmemberfunctions
Although every member must be declared inside its class, we can define a member function's body either outside or inside the class body. 
In `Sales_data`, `isbn` is defined inside the class: the other two will be defined elsewhere. 

We'll start by looking at the `isbn` function, which returns a `string` and has an empty parameter list: 

`std::string isbn() const{return bookNo;`}

### Introducing `this`
#thiskeyword
Here we use the dot operator; to fetch the `isbn`. 
`total.isbn();`
When we call al member function, we do so on behalf of the object on which the function was called. 
In this case, it will be returning the object total's bookNo. 
They do this through the <u>implicit</u> parameter named `this`. 
When we call a member function, `this` is initialized with the address of the object on which the function was invoked. 

When we call `total.isbn();` 

The compiler passes the address of `total` to the implicit `this` parameter in `isbn`. 
It is as if the compiler rewrites this call as: 
```
// pseudo-code illustration of how a call to a member function is translated
Sales_Data::isbn(&total);
```
Calling the `isbn` member of `Sales_data` passing the address of `total`. 

Inside a member function, we can refer directly to the members of  the object on which the function was called. We do not have to use a member access operator use the members of the object to which `this` points. 
Any direct use of a member of the class is assumed to be an implicit reference through `this`. 
That is, when `isbn` uses `bookNo`, it is implicitly using the member to which `this` points. It's as though we have written `this->bookNo`.

`this` parameter is defined for us implicitly. It would be illegal for us to define a parameter or a variable named `this` too. 

Inside the body of a member function, we can use `this`. 
It would be legal, although unnecessary, for us to define `isbn` as: 
`std::string isbn()const(return this->bookNo;}`

`this` is a `const` pointer. We cannot change the address that `this` holds. 

The type of `this` in a `Sales_data` member function is `Sales_data *const` 
`this` is implicit but follows normal initialization, which means that (by default) we cannot bind this to a `const` object. 
This fact, in turn means that we cannot call on ordinary member function on a `const` object. 

If `isbn` were a normal function, if if `this` were an ordinary pointer paramter, we would declare `this` as `const Sales_data * const`. After all, the body of `isbn` doesn't change the object to which `this` points. 

If we put `const` after the parameter list, it indicates that `this` is a pointer to a const. 

They would be considered const member functions. 
[[TOREREAD/const Member Functions]]
```
// pseudo code illustration of how the implicit this pointer is used
// this code is illegal, we may not explicilty define the this pointer ourselves
// note that this is a pointer to a const because isbn is a const member

std::string Sales_data::isbn(const Sales_data *const this)
{ return this->isbn;}
```

The fact that `this` is a pointer to `const` means that `const` member function cannot change the object on which they are called. 
`isbn` may read but not write to the data members of the objects on which it is called.

Objects that are `const`, references of pointers to `const` can called member functions that are `const`.

ChatGPT says it c an be called on both `const and non-const` objects. 
The idea with them is that they "promise" not to modify the state of the object on which they are called. Ensuring the integrity of objects in the program and enabling certain optimizations (of which I cannot speak to): 
```
class MyClass { 
	private: 
		int value
	
	public:
		MyClass(int val) : value(val) {}

		// non-const member fucntion 
		void setValue(int val){ 
			value = val;
		}

		// Const member function
		int getvalue() const { 
			// value = 10 would be an error as cannot modify value in const member function
			return value;
		}
};

int main(){ 
	const MyClass obj(5); // creating a const object

	
	// obj.setValue(10); // error as cannot modify a const object
	cout << "Value:  " << obj.getValue() << endl;
}
```

It's like the object becomes a `const` for this member function. 

Like the book said, that a `const` object cannot call a `non_const` member function, this would violate the `const` correctness principle. 


Changing some aspects to show how `this` works: 
```
void setValue(int val){ 
	this->value = val;
}

int getValue() const { 
	return this->value;
}

void printAddres() const{ 
	cout << "Address of this: " << this << endl;
	// prints out an address
}
```
Remember that this is a `const` pointer, that is top-level pointing. `this` is just a pointer, pointing to the object that we are calling member functions on. 

Here is a cool idea that happened earlier in the book when we had the same function that was overloaded due to passing a const, however, we just passed to the `non-const` function by `const_cast`;
```
class Foo
{ 
	private: 
		int foo: 
	
	public: 
		const int& get() const{ 
			//non trivial work
			return foo;
		}

		int& get(){ 
			return const_cast<int&>(const_cast<const Foo*>(this)->get());
		}
};
```


Overloaded member functions here. 
They both return a reference to an int, but the const version returns a `consat int&`. 

The `const_cast<int&>(const_cast<const Foo*>(this)->get());`
Looks a little confusing. 
Work from inside out. We are casting our `this` pointer to cast to a `const foo`  object, then we are dereferencing using the -> and calling get, to which we get a `const int&`. 
Therefore we have to `const_cast` this as well to a `non_const` reference. 

This idea: 
```
			MyClass* returnThis(){ 
				return this;}

int main(){ 
	MyClass obj1(5);
	auto ptr = obj1.returnThis();
	cout << ptr << endl;
	// output looks something like this : 0x61ff08
}
```



### Class Scope and Member Functions
Class is a scope itself, the definitions of the member functions of a class are nested inside the scope of the class. 
It's worth noting that `isbn` can use `bookNo` even though `bookNo` is defined after `isbn`. 

The compiler processes classes in two steps - the member declarations are compiled first, after which the member function bodies, if any, are processed. 

Therefore member function bodies may use other members of their class regardless of where in the class those members appear. No idea of forward and back declaration in a class. 

