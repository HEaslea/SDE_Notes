We are looking at how to tailor the copying, assigning, moving or destroying of an object. 
The copy constructor, move constructor, copy-assignment operator, move-assignment operator and destructor. 

They are copy-control members, most classes just default and compile synthesize them. For some classes that is a disaster, therefore we would need to define them ourselves. 

### The Copy Constructor 
#copysconstructor
A constructor is the copy constructor if its first parameter is a reference to eth class type and any additional parameters have default values: 
```
class Foo{ 
public: 
	Foo(); // default constructor

	Foo(const Foo&); // copy constructor
	//...
};
```
For reasons we'll talk about later, the first parameter must be reference type. 
The parameter is almost always a reference to `const`, although we can use a `nonconst`. 
It is used, in several circumstances, implicitly, therefore, the copy constructor should not usually be `explicit`. 

###### Synthesized Copy Constructor
Unlike the synthesized default constructor, a copy constructor is synthesized eveen if we defined other constructors. 

Some synthesized copy constructors prevent us from copying objects of that class type. 
It member-wise copies the members of its argument into the objects being created. 
The compiler copies each non`static` member in turn from the given object into the one being created. 

The type of each member is determined how it is copied over; Members of a class type are copied by the copy constructor for that class; members of built-in type are copied directly. 
Although we cannot directly copy an array, then synthesized copy constructor copies members of array type by copying each element. 
Elements of a class type are copied by using the element's copy constructor. 

The synthesized copy constructor for our `Sales_data` class is equivalent to: 
```
class Sales_data{ 
public: 
	// other members and constructor as before
	// declaration equivalent to the synthesized copy constructor
	Sales_data(const Sales_data&);
private: 
	std::string bookNO; 
	int units_sold = 0; 
	double revenue = 0.0;
};
// equivalent to the copy constructor that would be synthesized for Sales_data
Sales_data::Sales_data(const Sales_data& orig) : 
	bookNo(orig.bookNo), // uses the string copy constructor
	units_sold(orig.units_sold); // copies directly the orig
	revenue(orig.revenue) // copies directly orig double revenue
	{ } // empty body, could say copied here
```


### Copy Initialization
 We are now in a better position to fully understand the differences between direct and initialization and copy initialization
Remember this idea
```
string dotrs(10, '.'); // direct initialization
string s(dots); // direct initialization
string s2 = dots; // copy initialization
string null_book = "9-999-99999-9"; // copy initialization
string nines = string(100,'9'); // copy initialization
```

We we use direction initialization, we are asking the compiler to function match the best constructor with the arguments that we are providing. 
When we use copy initialization, we are asking for the right-hand operand to be copied into the object being created, converting the operand if necessary!
Direct initialization does not engage in narrowing conversions. 

Copy initialization normally uses the copy constructor, but, if a class has a move constructor, then copy initialization sometimes uses the move constructor instead. 
So for now, consider that copy initialization might use the copy constructor or the move constructor. 


Copy initialization happens not only when we define variables using an =, but also when we: 
#whendowecopy
- pass an object as an argument to a parameter of nonreference type
- Return an object from a function that has a nonreference return type
- Brace initialize the elements in an array or the members of an aggregate class. 

Some class types also use copy initialization for the objects they allocate. 
Some library containers do that to initialize the container, or when we call `insert` or `push` members. 
`emplace` uses direct initialized. 


#### Parameters and Return Values
During a function call, parameters that have a nonreference type are copy initialized. 
Similarly when a function has a nonreference return type, the return value is used to copy initialized the result of the call operator at the call site. 
The fact that the copy constructor is used to initialize nonreference parameters of a class type explains why its own parameter must be a reference. 
If it weren't, it would be cyclical, constantly calling itself. 

### Constraints on Copy Initialization
Whether we use copy or direct initialization matters if we use an initializer that requires conversion by an `explicit` constructor. 
```
vector<int> v1(10); // ok; direct initialization
vector<int> v2 = 10; // error, constructor that takes a size is explicit

void f(vector<int>); // f's parameter is copy initialized

f(10); // error; can't use an explicit constructor to copy an argument

f(vector<int>(10)); // ok : directly construct a temporary vector from an int
```

Directly initializing v1 is fine, however, v2 is an error as the `vector` constructor that takes a single size parameter is `explicit`. 

```
class MyClass {
public:
    explicit MyClass(int x) {
        // Constructor code
    }
};

int main() {
    MyClass obj1 = 42; // Error: Implicit conversion not allowed
    MyClass obj2(42);  // OK: Direct initialization
    return 0;
}

```
##### The Compiler can Bypass The Copy Constructor
During copy initialization, the compiler is permitted (but not obligated) to skip the copy/move constructor and create the object directly. 
The compiler is able to rewrite: 
```
string null_book = "9-999-99999-9"; // copy initialization
into 
string null_book("9-999-99999-9"); // compiler omits the copy constructor
```
Even if the compiler omits that call to the copy constructor/move constructor, the copy/move constructor must remain public and available. 


### The Copy=Assignment Operator
```
Sales_data trans, accum;
trans = accum; // uses the Sales_data copy-assignment operator
```

As with the copy constructor, the compiler synthesizes a copy-assignment operator if the class does not define its own. 

##### Introducing Overloaded Assignment
These are functions that have a name `operator` followed by the operator symbol. 
The assignment operator is a function named `operator=`. 
The parameters in an overloaded operator represent the operands of the operator. 
When an operator is a member function, the left hand operand is bound to the implicit `this` parameter. 
The right hand operand in a binary operator, such as assignment is passed as an explicit parameter. 

```
class Foo{ 
public: 
	Foo& operator=(const Foo&); // assignment operator
	// ... 
};
```
They usually return a reference to their left hand operand. 
Assignment operators ordinarily should return a reference to their left hand operand. 

##### Synthesized Copy-Assignment Operator
If the class doesn't define it, the compile will for us again. 

Analogously to the copy constructor, for some classes the synthesized copy-assignment operator disallows assignment. 
Otherwise, it assigns each non`static` member of the right hand object to the corresponding member of the left hand object using the copy-assignment for the type of that member. 
Array members are assigned by assigning each element of the array. 
This will still return a copy-assignment operator returns a reference to its left hand object. 

This might be the equivalent of `Sales_data` copy assignment operator: 
```
Sales_data& Sales_data::operator=(const Sales_data &rhs)
{ 
	bookNo = rhs.bookNo; // calls the string::operator=
	units_sold = rhs.units_sold; // usese built in int assignment
	revenue = rhs.revenue; // uses built in double assignment
	return *this; 
}
```

[[The Destructor]]

##### `private` Copy Control
In the olden days, before we just `delete`d everything, we used to make sure that we can't copy, by declaring copy constructor and copy-assignment as `private`: 
```
class PrivateCopy{ 
	// no access specifier; following is all private
	// copy control is prviate and so is inaccessible to ordinary user code

	PrivateCopy(const PrivateCopy&);
	PrivateCopy& operator=(const PrivateCopy&); 
	// other members

public: 
	PrivateCopy() = default; // use the synthesized default constructor
	~PrivateCopy(); // users can define objects of this type but not copy them
};
```

As the `destructor` is `public`, we will be able to define `PrivateCopy` objects. 

As copy constructor and copy assignment operator are `private`, user code will not be able to copy such objects. 
Friends and members of the class will be able to make copies. 
Therefore these members will have to be `private` too. 

An attempt to use an undefined member results in a link-time failure. 
User code here that try to make a copy will be flagged as an error at compile time. 

The better practice here, instead of making copy constructor and copy-assignment operators `private`, we should say `= delete`. 

[[Copy Control and Resource Management]]

[[Swap]]
[[Assignment and Swap]]

### A Copy-Control Example
Some classes have bookkeeping or other actions that the copy-control members must perform. 
We might look at a mail handling application. 
`Message` and `Folder`, representing emails and their directories. 
Each `Message` can be in multiple `Folder`s
However, there will be only one copy of the content of any given `Message`. 
If the contents are then changed, all of that message in all the folders will be changed with it. 
Each `Message` will store a `set` of pointers to the `Folders` in which it appears, each `Folder` will contain a `set` of pointers to its messages. 

![[Pasted image 20240414135301.png]]

The `Message` class will provide a `save` and `remove` operation to add or remove a `Message` from a `Folder`. 
To create a new Message, we will create the contents, and **not** specify the folder. 

To put a `Message` in a `Folder` we must call `save`. 

When copying a `Message`, the copy and the original will be distinct `Messages`, but both should appear in the same `set` of `Folder`s. 
Copying will copy the contents and the `set` of `Folder` pointers. 
It must also add a pointer to the newly created `Message` to each of those `Folder`s. 

When we destroy a `Message` we have to get rid of all the pointers as well. 

When assigning one `Message` to another, we'll replace the `contents` of the left-hand `Message` with that of the right. We have to update the `set` of `Folder`s removing the left hand `Message` from its previous `Folder`, and adding to that `Message` to the `Folder` in which the right hand `Message` appears. 

```
class Message{ 
	friend class Folder; // when no inheritance, but access still

public: 
	// folder is innitialized to empty set
	explicit Message(const std::string& str = ""): 
		contents(str){}

	// copy control to manage pointers to this Message
	Message(const Message&); // copy constructor
	Message& operator=(const Message&); // copy assignment
	~Message(); // destructor

	// add/remove this Message from the spec'd Folder set of messages
	void save(Folder&);
	void remove(Folder&);
	
private: 
	string contents; // actual message text
	set<Folder*> folders; // Folders that have this message
	// utility functions used by copy constructors, assignment, destructor
	// add this Message to the Folders that point to the parameter
	void add_to_Folders(const Message&); 

	// remove this message from every Folder in folders
	void remove_from_Folders();
};
```

`contents` storing the message, and `folder`, to store pointers to the `Folder`s in which this `Message` appears. 

The constructor takes a `string` and copies it into `contents`, and implicitly initializes `folders` to the empty set. 

```
void Message::save(Folder& f)
{ 
	folders.insert(&f); // add the given Folder to our list of folders
	f.addMsg(this); // add this Message to f's set of Messages
}

void Message::remove(Folder& f)
{ 
	folders.erase(&f); // taking folder out of list
	f.remMsg(this); // remove this Message to f's set of Messages
}
```
Simple. 
Then, the Folder object itself has to remove or add the message too, not just from the `Message`'s end. 

When we copy a `Message`, the copy should appear in the same `Folder` as the original. 
We must go through the `set` of `Folder` pointers adding a pointer to the  new `Message` to each `Folder` that points to the original `Message`. 
Both the copy constructor and the copy-assignment operator will need to do this work. 
It will  be easier then to get a common function to do this processing: 
```
// add this Message to Folders that point to m
void Message::add_to_Folders(const message& m)
{ 
	for(auto f : m.folders)// for each Folder that holds m
		f->addMsg(this); // add a pointer to this Message to that folder
}
```
Here we call the `addMsg` on each `Folder` in `m.folders`. 
The `addMsg` will add a pointer to this `Message` to that `Folder`. 

The copy constructor copies the data members of the given object: 
```
Message::Message(const Message& m): 
	contents(m.contents), folder(m.folders)
{
	add_to_Folders(m); // add this Message to the Folders that point to m
}
```

#### The `Message` Destructor
When a `Message` is destroyed, we must remove it from the `Folders` that point to it. 
This is shared with the copy-assignment operator, we'll define a common function to do it: 
```
void Message::remove_from_Folders()
{ 
	for(auto f : folders)// for each pointer in folders
		f->remMsg(this); // remove this Message from that folder
	folders.clear(); // no Folder points to this message
}
```
Given this; the destructor is fairly trivial: 
```
Message::~Message()
{ 
	remove_from_Folders();
}
```

This means that no `Folder` has a pointer to the `Message` we are destroying. 
The compiler will automatically invoke the `string` destructor to free `contents` and the `set` destructor to clean up the memory used by those members. 

They aren't pointers to the `content (string)` and `folders (set of Folder pointers)`, so we don't need to get rid of allocation here. 

#### `Message` Copy-Assignment Operator
Make sure that we are safe against self-assignment
```
Message& Message::operator=(const message& rhs)
{ 
	// handle the self-assignment by removing pointersa before inserting them 
	remove_from_Folders(); // update existing Folders
	contents = rehs.contents; // copy the message content from rhs
	folders = rhs.folders; // copy Folder points from rhs
	add_to_Folders(rhs); // add this message to those Folders
	return *this;
}
```

IF the left and right are the same object, then they have the same address. 

#### The `swap` Function for `Message`
Remember that with our `swap` we need the library for `string` and `set`. 
Therefore, our message will benefit from using its own `swap`. 
If we just use `std::swap(Message& m1, Message& m2)` then god knows what is going on, therefore, let's just define our own, using the lib `swap` for smaller details. 
We'll manage the `Folder` pointers by making two passes through each of the `folder`s members. 
The first will remove the `Messages` from their respective `Folders`. 
then we call `swap` to swap the data members. 
We'll make a second pass through `folder`s this time adding pointers to the swapped `Message`s: 
```
void swap(Message& lhs, Message& rhs)
{ 
	using std::swap; // not strictly needed here, but good habit
	// remove pointers to each Message from their respective Folders
	for(auto f : lhs.folders)
		f->memMsg(&lhs);
	for(auto f : rhs.folders)
		f->remMsg(&rhs);
	// swap the contents and Folder pointer sets
	swap(lhs.folders, rhs.folders); // uses swap(set&, set&)
	swap(lhs.contents, rhs.contents); // uses swap(string&, string&)
	// add pointers to each Message to their(new) respective Folders
	for(auto f : lhs.folders)
		f->addMsg(&lhs);
	for(auto f : rhs.folders)
		f->addMsg(&rhs);
}
```
