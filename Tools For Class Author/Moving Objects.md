[[Classes That Manage Dynamic Memory]]
Move rather than copy an object; 
Copies are made in many circumstances, in some cases an object is immediately destroyed after it is copied. 
So in situations like that it is much easier to move rather than copy obviously. 

During `reallocation` there is no need to copy - rather than move  - the elements from the old memory to the new. 

A second reason to move, that is more necessary is that such classes like IO or `unique_ptr`, can't be copied (it's the law). 
We can only `move` here, there is no chance to copy. 

In earlier versions of the language, there was no way of doing this, we had to make a copy, which can be super expensive. 

Lib Containers, eg. `string` `shared_ptr` classes support move as well as copy. 
IO and `unique_ptr` classes can only  be moved. 

### Rvalue References
#rvalues #rvaluereferences
An rvalue reference is a reference that must be bound to an rvalue. 
This is obtained using `&&` rather than `&`. 
Rvalue references have the important property that they may be bound only to an object that is about to be destroyed. 
As a result, we are free to `move` resources from an rvalue reference to another object. 

Lvalue and Rvalue are properties of an expression. 
Some expression yield or require lvalues; others yield or require rvalues. 

Generally speaking, an lvalue expression refers to an object's identity whereas an rvalue expression refers to an object's value. 

An rvalue ref is just another name for an object. 
We cannot bind regular references - which we'll refer to as lvalue refs when we need to distinguish them from rvalue references - to expression that require a conversion, to literals, or to expression that return an rvalue. 
Rvalue refs have the opposite binding properties: We can bind an rvalue ref to these kinds of expression, but we cannot directly bind an rvalue ref to an lvalue.

Basically, you can't do something like this: 
```
string a = "Hello World";

string && f = a; // ERROR
// Rvalue Ref cannot be bound to an lvalue

string && f = "Hugo"; // is fine
```

```
int i = 42; 
int &r = i; // Ok r refers to i, an lvalue.

int && rr = i; // error cannot bind an rvalue ref to an lvalue

int & r2 = i*42; // error i *42 is an rvalue (temp value)

const int & r3 = i * 42; // ok we can bind ref to const to an rvalue

int && rr2 = i * 42; // ok rvalue from multiplication
```

Functions that return lvalue references, along with the assignment, subscript, dereference, and prefix increment/ decrement operators, are all examples of expression that return lvalues. 

Functions that return nonreference type, along with the arithmetic, relational, bitwise, and postfix increment/decrement operators, all yield rvalues. 
We cannot bind an lvalue reference to these expressions, but we can bind either an lvalue reference to `const` or an rvalue reference to such expressions. 
This means that returning from a function, that value is temporary, we have to assign it to something in order to gain access to it again. 

#### Lvalues Persist; Rvalues are Ephemeral
Lvalues have a persistant state, rvalues are either literals or temp objects created in the course of evaluating expressions: 

As rvalue references can only be bound to temporaries, we know that: 
- The referred-to object is about to be destroyed
- There can be no other users of that object

These facts together do mean that code that uses an rvalue reference is free to take over resources from the object to which the reference refers. 

Rvalue references refer to objects that are about to be destroyed. 
Hence, we can "steal" state from an object bound to an rvalue reference. 

#### Variables are Lvalues
A variable is an expression with one operand and no operator. 
Like any other expression, a variable expression has the lvalue/rvalue property. 
Variable expressions are lvalues. 
As a consequence of this, we cannot bind an rvalue reference to a variable defined as an ravalue reference type: 
```
int && rr1 = 42; // ok literals are rvalues
int && rr2 = rr1; // error as rr1 is no an lvalue
```

A variable has to be an lvalue, as it exists until it goes out of scope. 

A variable is an lvalue; we cannot directly bind an rvalue ref to a variable even if that variable was defined as an rvalue reference type. 

Ie, you can't do something like this: 
```
string & f = "Hello World";
```

#### The Lib `move` Function
We can explicitly cast an lvalue to its corresponding rvalue reference type. 
We can also obtain an rvalue reference bound to an lvalue by calling a new lib function named `move`. `<utility>` header. 

The `move` function, allows us to return an rvalue reference to its given object. 
`int && rr3 == std::move(rr1); // ok`

Calling `move` tells the compiler that we have an lvalue that we want to treat as if it were an rvalue. 
The call to `move` promises that we do not intend to use `rr1` again except to assign to it or destroy it. 

After a call to `move`, we cannot make any assumptions about the value of the moved from object. 

We can destroy a moved-from object and can assign a new value to it, but we cannot use the value of a moved-from object. 

Code that uses `move` should use `std::move` not move. Doing so avoids potential name collisions. 


#### Move Constructor and Move Assignment
We have to define a move constructor in order to get this going. 
They are similar to the corresponding copy operations, but they "steal" resources from their given object rather than copy them. 

Like the copy constructor, the move constructor has an initial parameter that is a reference to the class type. 
The reference parameter, there though, is an rvalue reference. 
In addition to moving resources, we have to make sure that the moved-from object is left in a destructable state. 
Once its resources are moved, the original object must no longer points to those moved resources - responsibility for those resources has been assumed by the newly created object.

Here we define the move constructor to move rather than to copy elements from one `StrVec` to another: 
```
StrVec::StrVec(StrVec&& s) noexcept // move won't throw any exceptions
// member initializers take over the resource in s
 : elements(s.elements), first_free(s.first_free), cap(s.cap)
{ 
	// leave s in a state in which it is safe to run the destructor on
	// destructor is run when things leave scope
	s.elements = s.first_free = s.cap = nullptr;
}
```
We'll explain the use of `noexcept` (which signals that our constructor does not throw any exception) shortly, but let's first look at what this constructor does: 

This constructor does not allocate new memory; it takes over the memory in the given `StrVec`, having taken over this memory from its argument, the constructor body sets the pointers in the given object to `nullptr`. 
After an object is moved from, that object continues to exist. 
Eventually that object will be destroyed, meaning that the destructor will be run on that object. 
The `StrVec` destructor calls `deallocate` on `first_free`. If we neglected to change `s.first_free` then we would be destroying the moved-from object would delete the memory we just moved. 

#### Move Operations, Lib Containers, and Exceptions
As move "steals" resources, it usually doesn't allocate any resources. 
Move operations usually will not throw exceptions. 

When we write a move operation that cannot throw, we should inform the library of that fact. 
As we'll see, unless the library knows that our move constructor won't throw, it will do extra work to cater to the possibility that moving an object of our class type might throw. 

`noexcept` will be covered even further as we go on. 

For now, we all we need to know is that `noexcept` is a way for us to promise that our function does not throw any exceptions. 
To look something like this: 
```
class StrVec{ 
public: 
	StrVec(StrVec&&) noexcept; // move constructor
	// other members as before
};
StrVec::StrVec(StrVec &&s) noexcept;  /* member intiailizing */
{ /* constructor body */}
```

We have to `noexcept` both the declaration and the definition. 

Understanding why we do this is super helpful for out understanding of how the library interacts with objects of the types we write. 

We need to indicate that a move operation will not throw because of two interrelated facts: 
1. Although move operations usually don't throw exceptions, they are permitted to do so. 
2. The library containers provide guarantees as to what they do if an exception happens. 
Eg. `vector` guarantees that if an exception happens when we call `push_back`, the `vector` itself will be left unchanged. 

Think about what happens in `push_back`. The `push_back` might require that the `vector` be reallocated.
Moving all the elements to new space in memory, just as we did in `reallocate` in our own written functions. 

Remember that move usually changes the value of the moved-from object. 
If reallocation uses a move constructor and that constructor throws an exception, then there would be a problem. 
The moved-from elements in the new space would not yet exist.
In this case, `vector` would be unable to meet its requirement that the `vector` is left unchanged. 

On the other hand, if `vector` used a copy constructor and an exception was thrown, it can easily meet this requirement. 
Here, the elements are being constructed in the new memory, the old elements remain unchanged (this is not true of the move idea, where we are changing the old, by moving everything to the new, what will the old look like?). 
If an exception happens now, then `vector` can free the space it allocated (but could not successfully construct) and return. 
The original `vector` elements remain the same and still exist in this instance. 

To avoid this potential problem, `vector` has to use the copy constructor instead of a move constructor during reallocation "unless" it knows that the element type's move constructor cannot throw an exception. 
If we want objects of our type to be moved rather than copied in circumstances such as a `vector` reallocation, we must explicitly tell the lib that our move constructor is safe to use. 
This is done using `noexcept`. 

##### Move-Assignment Operator
This does the same work as the destructor and the move constructor. 

As with the move constructor, if our move-assignment doesn't throw any exceptions, then we should mark it as `noexcept`. 
Again, like the copy-assignment, the move-assignment must make sure that it guards against self-assignment. 

```
StrVec& StrVec::operator=(StrVec&& rhs) noexcept
{ 
	// direct test for self-assignment
	if(this != &rhs){ 
		free(); // free existing elements
		elements = rhs.elements; // take over resources from rhs
		first_free = rhs.first_free;
		cap = rhs.cap;
		// leave rhs in a destructible state
		rhs.elements = rhs.first_free = rhs.cap = nullptr;
	}
	return *this;
}
```
In this case we check that `this` and the address of `rhs` are the same. If they're the same, we just return `*this`. 
As in the move constructor, we set the pointers of `rhs` to `nullptr`s. 
We do have to check that `lhs` is the same as the `rhs`. 


##### A Moved-from Object must be Destructible
Moving from an object does not destroy that object: 
Sometime after the move operation completes, the moved-from object will be destroyed. 
Therefore, when we write a move operation, we must ensure that the moved-from object is in a state in which the destructor can be run. 
Our `StrVec` move operations meet this requirement by setting the pointer members of the moved-from object to `nullptr`. 

Move operations have to leave that object valid. A valid object is one that can safely be given a new value or used in other ways that do not depend on its current value. 
On the other hand, move operations have no requirements as to the value that remains in the moved-from object. 
As a result, we should never depend on the value of a moved-from object. 

If you imagine a `string` that will be moved from, we know that it remains valid, after it has been moved from. As a result, we can run operations such as `empty` or `size`. We don't know what they'll result in, however, it means that the object is still valid and gives a good idea of what valid means. We might expect that empty will return that the `string` is actually empty, however that is not guaranteed. However, it does mean that the `string` is still a valid object, so that we can destroy it later. 

Our `StrVec` move operations leave the moved-from object in the same state as a default initialized object. 
Therefore, all the operations of `StrVec` would still work on this object, although we are also defining how that would work as well here, we know how it would work if we used some of the other functions, however, with other classes, in which we didn't define move operations. 
#### The Synthesized Move Operations
#synthesizedmoveoperations
The conditions, for which we synthesize move operations are quite different than that of copy and copy assignments. 
The compiler will **always synthesize copy operations**. 
They are defined to be memberwise copy or assign the object or they are defined as deleted functions, depending on inheritance etc. 

For some classes, the compiler will not synthesize the move operations **at all**. 
If a class defines its own copy constructor, copy assignment operator, or destructor, the move constructor and move assignment operator are not synthesized. 
Therefore, some classes forego move operations. 
The corresponding copy operation is used in place of move through normal function matching. We'll see this more later in the book. 

The compiler will synthesize a move constructor or a move-assignment operator **only** if the class doesn't define any of its own copy-control members and if every non`static` data member of the class can be moved. 

The compiler will be able to move built-in types. 

```
// The compiler will syntheisze the move operations for X and hasX

struct X{
	int i; // built in types can be moved
	std::string s; // string defines its own move operations
};

struct hasX{
	X mem; // X has synthesized move operations
};

X x, x2 = std::move(x); // uses the synthesized move constructor

hasX hx, hx2 = std::move(hx); // uses the synthesize move constructor
```

> The compiler synthesizes the move constructor and move assignment only if a class does not define any of its own copy-control members and only if all the data members can be move constructed, and move assigned, respectively. 

A move operation is implicitly defined as a deleted function. 
We explicitly ask the compiler to generate a move operation by using `= default`, and the compiler will not be able to move, on its own, all the members, then it will have to resort to defining the move operation as deleted. 

The compiler will define the move operations as deleted for the similar reasons that it will do for the copy constructors. 
So they are defined as deleted if the destructor is deleted or inaccessible. 

If we imagine Y as a class: 
```
// Y is a class that defines its own copy constructor but not a move constructor
struct hasY{ 
	hasY() = defult;
	hasY(hasY&&) = default;
	Y mem; // hasY will have a deleted move constructor
};

hasY hy, hy2 = std::move(hy); // error as move constructor is deleted
```

Compiler will be able to copy object of type Y, but won't be able to move them. 
`hasY` has explicitly requested a move constructor  (by default), which the compiler says no to, and doesn't do one for us. 
Hence `hasY` will have a deleted move constructor. 
Had `hasY` omitted the declaration of its move constructor, then the compiler would not synthesize `hasY` move constructor at all. 
The move operations are not synthesized if they would otherwise be defined as deleted. 

Whether a class defines its own move operations has an impact on how the copy operations are synthesized. 
If the class defines either a move constructor and/or a move-assignment operator, then the synthesized copy constructor and copy-assignment operator for that class will be defined as deleted. 

> Classes that define a move constructor or move-assignment operator must also define their own copy operations, otherwise they are deleted by default. 


#### Rvalues are Moved, Lvalues are Copied...
When a class has both a move constructor and a copy constructor, the compiler uses ordinary function matching to determine which constructor use. 
Similarly for assignment too. 

In our `StrVec` class, the copy version take a reference to a `const StrVec`. 
They can be used on any type that can be converted to `StrVec`. 
The move versions take a `StrVec&&` and can be used only when the argument is a `nonConst` rvalue: 
```
StrVec v1, v2;
v1 = v2;  // v2 is an lvalue; copy assignment used

StrVec getVec(istream &); // getVec returns a rvalue; a temp StrVec
v2 = getVec(cin); // getVect(cin) is an rvalue; move assignment here
```

The first assignment, we pass `v2` to an assignment operator. 
The type of `v2` is a `StrVec`, and the expression, `v2` is an lvalue. 
The move version of assignment is not viable, because we cannot implicitly bind an rvalue reference to an lvalue. 
This would have to be using the copy assignment. 

The second assignment, we assign from the result of the `getVec`. 
The expression is an rvalue. 
Both assignment operators here are viable, we can bind an rvalue to a const ref to something. 
The calling of the copy assignment here would require a conversion to `const`, whereas `StrVec&&` is an exact match. 
The second one matches more, therefore we use that one. 

#### Rvalues will be Copied if there is No Move Constructor
A class has a copy constructor, no move constructor, the compiler will not synthesize the move constructor. 
If no move constructor, then function matching ensures that objects of that type are copied, even if we attempt to move them by calling `move`: 
```
class Foo{ 
publci: 
	Foo() = default;
	Foo(coinst Foo&); // copy constructor
	// other members, but Foo does not define a move constructor
};

Foo x; 
Foo y(x); // copy constructor, x is an lvalue
Foo z(std::move(x)); // copy constructor, as there is no move constructor
```

The call to move `x` means that we get the `Foo&&` bound to `x`.  The copy constructor for `Foo` is viable because we can convert `Foo&&` to a `const Foo&`. 

Using the copy constructor, in place of the move constructor is almost surely safe (and similarly for the assignment operators). 
In this case, the copy constructor will leave the "want-to-be-moved-from" value completely safe. 

> If a class has a usable copy constructor and no move constructor, objects will be "moved" by the copy constructor. Similarly for the copy assignment operator and move assignment operator. 


### Copy-and-Swap Assignment Operators and Move
In `HasPtr` we had the copy-and-swap assignment operator, and it will be used as a good illustration of the interaction between function matching and move operations. 

Let's add a move constructor and a move assignment as well. 

```
class HasPtr{ 
public: 
	// added a move constructor
	HasPtr(hasPtr &&p) noexcept: ps(p.ps), i(p.i) { p.ps = 0; }

	// assignment operator is both the move- and copy assignment operator
	HasPtr& operator=(HasPtr rhs)
		{ swap(*this, rhs); return *this;}

	// other members from earlier
};
```
In this version, we've added a move constructor that takes over the values from its given argument. 
The constructor body sets the pointer member of the given `HasPtr` to 0 to ensure that it is safe to destroy the moved-from object. 

Nothing it does can throw an exceptions, marked therefore as `noexcept`. 

The assignment operator, which has a nonreference parameter, which means the parameter is copy initialized. 
Depending on the type of argument, copy initialization uses either the copy constructor or the move constructor; lvalues are copied, and rvalues are moved. 
As a result, this single assignment operator acts as both the copy-assignment and move-assignment operator: 
Eg. assuming both `hp, hp2` are `HasPtr` objects: 
```
hp = hp2; // hp2 is an lvalue, copy constructor used to copy hp2
hp = std::move(hp2); // move constructor moves hp2
```
In the first, the rhs operand is an lvalue, so the move constructor is not viable. 

The copy constructor will be used to initialize `rhs`. 
It will allocate a new `string` and copy the `string` to which `hp2` points. 
We can use `swap` here as the `rhs` will be destroyed after it goes out of scope, so it doesn't really matter what it holds. 

>**Updating the Rule of Three**
>All five copy-control members should be thought of as a unit: 
>Ordinarily, if a class defines any of these operations, it should usually define them all. Some classes have to define the copy constructor, copy assignment operator, and destructor to work correctly. 
>They typically have a resource that the copy members must copy. 
>Copying a resource entails some amount of overhead.
>Classes that define the move constructor and move assignment operator can avoid this overhead in those circumstances where a copy isn't necessary. 


##### Move Operations for the Message Class
Defining our copy constructor and copy-assignment operator generally also benefit by defining the move operations. 
`Message` and `Folder` classes should define move operations as well. 

The `Message` class can use the `string` and `set` move operations to avoid the overhead of copying the `contents` and `folders` members. 
In addition to moving the `folders` members, we update each `Folder` that points to the original `Message`. 
We must remove pointers to the old `Message` and add a pointer to the new one. 
There is common work here, so we need a function here to do that, so we don't have to writ the same thing over and over. 

```
// move the Folder pointers from m to this Message
void Message::move_Folders(Message *m)
{ 
	folders = std::move(m->folders); // uses set move assignment
	for(auto f : folders){  // for each Folder
		f->remMsg(m); // remove the old Message from the Folder
		f->addMsg(this); // add this Message to that folder
	}
	m->folders.clear(); // ensure that destroying m is harmless
}
```

This begins by moving the `folders` set. 
By calling `move`, we use the `set` move assignment rather than its copy assignment. 
Had we omitted the call the `move`, this would still work, but the copy is unnecessary. 

It's worth nothing that inserting an element to a `set` might throw an exception - adding an element to a container requires memory to be allocated, we might throw a `bad_alloc` exception. Therefore we cannot mark these functions as `noexcept`. 

The function ends with calling `clear` on `m.folders`. 
After the `move` we know that `m.folders` is valid but have no idea what its contents are. 

The `Message` move constructor calls `move` to move the `contents` and default initializes its `folders` member: 
```
Message::Message(Message &&m): contents(std::move(m.contents))
{ 
	move_Folders(&m); // move folders and updates the Folder pointers
}
```

The move-assignment operator does a direct check for self-assignment: 
```
Message& Message::operator= (Message && rhs)
{ 
	if(this != rhs){ // direct check for self-assignment
		remove_from_Folders();
		contents = std::move(rhs.contents); // move assignment
		move_Folders(&rhs); // reset the FOlders to point to this Message
	}
	return *this;
}
```

I think the main thing here is that we are referring to something, using pointers, and in that thing that we are referring to,  we are referring back, therefore, when we destroy our objects, we need to delete the reference to the object in the hting that the object refers to. 

[[Move Iterators]]