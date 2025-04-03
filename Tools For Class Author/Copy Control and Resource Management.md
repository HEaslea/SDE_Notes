#copycontrol
#resourcemanagement
[[Copy Control]]

Destructors to free the resources allocated by the object. 
Once we need a destructor, then we usually (99% of the time) need a copy constructor and copy-assignment operator as well. 

We can define the copy operations to make the class behave like a value or like a pointer. 

Classes that behave like values have their own state. 
When we copy a value-like object, the copy and the original are independent of each other. 
Changes made to the copy have no effect on the original, and vice versa. 

Classes that act like pointers share states. When we copy objects of these, the copy and the original use the same underlying data, they point to the same thing. 
Changes made to the copy also change the original, and vice versa. 

Of what we have used so far, library containers `string` class have valuelike behaviour. 
`shared_ptr` provides pointer like behaviour, as does our `StrBlob` class. 
The IO types and `unique_ptr` do not allow copying or assignment, so they provide neither valuelike nor pointerlike behaviour. 


#### Classes that Act Like Values
Each object will have to have its own copy of the resource that the class manages. 
That means each `HasPtr`, in the eg, object must have its own copy of the `string` to which `ps` points. 
Implementing this means: 
- A copy constructor that copies the `string`, not just the pointer to it. 
- A destructor to free the `string`.
- A copy-assignment operator to free the object's existing `string` and copy the `string` from its right hand operand. 
This would look something like this: 
```
class HasPtr{ 
public: 
	HasPtr(const string& s = string()) : 
		ps(new string(s)), i(0) {}
	// each HasPtr has its own copy of the string to which ps points

	HasPtr(const HasPtr& p) : 
		ps(new string(*p.ps)), i(p.i) {}

	HasPtr& operator= (const HasPtr&); 
	

	~HasPtr(){ delete ps; }

private: 
	string *ps; 
	int i;
};
```
So here we have to make sure that the strings that we point to are unique for each of the objects of `HasPtr`. 

The constructor dynamically allocates its own copy of that `string` and stores a pointer to that `string` in `ps`. 
The copy constructor also allocates its own copy of the `string`, essentially the value of the pointer is different, it points to its own part of memory, that has its own string. 
The destructor frees the memory allocated in its constructors, using `delete ps;`. 


##### Value-like Copy-Assignment Operator
Assignment operators typically combine the actions of the destructor and the copy constructor. 
Assignment destroys the left hand operand's resources. 
Copy constructor, assignment copies data from the right hand operand. However it's crucial that these actions be done in a sequence. 
So first we copy the right hand side. After the copy is made, we'll free the left hand side and update the pointer to point to the newly allocated `string`: 
```
HasPtr& HasPtr::operator=(coinst HasPtr& rhs)
{ 
	auto newp = new string(*rhs.ps); // copy the underlying string
	delete ps; // free the old memory
	ps = newp; // copy data from rhs into this object
	i = rhs.i; // value copy
	return *this; // return this object
}
```

There are two points to keep in mind when we think of assignment operating: 
1. They must work correctly if an object is assigned to itself, leaving an object that is essentially identical to the original. 
2. Most assignment operators share work with the destructor and copy constructor. 

A good pattern here is to copy the right hand to a local temp. After the copy is done, safe to destroy the existing members of the left hand operand. 
Once the left hand is destroyed, copy the data from the temp into the members of the left hand operand. 

To illustrate the important of guarding against self-assignment, consider what would happen if we wrote the assignment operator as: 
```
// WRONG way to write
HasPtr& 
HasPtr::operator=(const HasPtr& rhs)
{ 
	delete ps; // freeing string to which the object points
	// if rhs and *this are are the same object, we're copying from deleted memory!
	// that's something that we have to deal with, assigning to itself¬
	ps = new string(*(rhs.ps));
	i = rhs.i;
	return *this;
}
```

If `rhs` and `this` are the same object, deleting `ps` frees the `string` to which both `*this` and `rhs` point. 

#### Defining Classes That Act Like Pointers 
In order to work, we have to copy the pointer, not that to which the pointers point. 
We cannot free, until the last `pointer` to that `string` has been lost. 
The easiest way to do this is `shared_ptr` to manage the resources in the class. 
Copying a `shared_ptr` copies the pointer to which the `shared_ptr` points. 

Meaning we have two separate locations with the same memory address. 

Sometimes we want to manage a resource directly. 
Such cases, it's useful to use a `reference count`. 
To show how this works, we'll redefine `HasPtr` to provide pointerlike behaviour, but we will do our own reference counting. 

#### Reference Counts
The easiest way to do this is to reference an `int` on the heap. 
Initialized to 1.
- Initialize the object, each constructor (other than copy constructor) creates a counter. This counter will keep track of how many objects we have that share state. 
- Copy constructor does not allocate a new counter; copies, including the counter. Then we increment the counter. 
- Destructor decrements the counter, indicating that there is one less user of the shared state. If 0, then the destructor deletes the state. 
- Copy assignment increments the right hand operands counter and decrement the counter of the left hand operand. If it goes to 0, then we destroy etc. 

The only wrinkle, is where to put reference count. 
The counter must be something that all objects can reference and can have access to. 

Remember that heap memory is close to global memory, that we have local/or private access to. 
Let's just point to the counter; 

###### Defining a Reference-Counted Class
This is more a pointer like version, sort of like `shared_ptr`, but our own version: 
```
class HasPtr{ 
public: 
	// constructor allocates a new string and a new counter, which it sets to 1
	HasPtr(const string& s = string()) : 
		ps(new string(s)), i(0), use(new size_t(1)) {}co

	// copy constructor copies all three data members and increments the counter
	HasPtr(const HasPtr& p) : 
		ps(p.ps), i(p.i), use(p.use) {++*use;}

	Hasptr& operator=(const HasPtr&);

	~HasPtr(); 

private: 
	string *ps;
	int i;
	std::size_t *use; // member to keep track of how many objects share *ps;
};
```

##### Pointerlike Copy Members "Fiddle" the Reference Count
We copy `ps` itself, not the string it points to, there is no point of copying the string, as `ps` just points us to it. 
Copy constructor must also increment. 

Destructor can't just destroy at will, a modicum of subtlety. 
If the counter does go to zero, then the destructor frees the memory to which both `ps` and `use` point. 

```
HasPtr::~HasPtr()
{ 
	if(--*use == 0){ 
		delete ps;
		delete use;
	}
}
```


As usual, the operator must be able to handle self assignment: 
```
HasPtr& HasPtr::operator=(const HasPtr& rhs)
// remember that lhs is passed in as implicit this
{ 
	++*rhs.use; // increment the use count of the RHS
	if(--*use == 0) { 
		delete ps; 
		delete use;
	}
	ps = rhs.ps; // copy data from rhs into this object
	i = rhs.i; 
	use = rhs.use;
	return *this; // return this object
}
```

[[Swap]]