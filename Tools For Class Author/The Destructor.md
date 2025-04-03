#thedestructor
Inverse to the constructors; Constructors initialize the non`static` data members of an object and may do other work;
destructors do whatever work is needed to free the resources used by an object and destroy the non`static` data members of the object. 
Has no return value and takes no parameters: 

```
class Foo{ 
public: 
	~Foo(); // destructor
};
```

It cannot be overloaded as it takes no parameters. 
There is always only one. 

##### What does it do? 
A destructor, like the reverse of the initialization and function body in the constructor, will have a destruction part and a function body too. 
Remember that members are initialized in the same order as they appear in the class. 

In a destructor the function body is executed first, and then the members are destroyed. 
Members are destroyed in reverse order that they were initialized in. 
Mostly, they just free allocated variables during the lifetime of the object. 

The destruction part is implicit, you cannot write something akin to the initialization list. What happens when it is, depends on the type of the member. 
They run by running the member's own destructor. 
Built-in types do not have destructors, so nothing is done to destroy members of built-in types. 

Implicit destruction of a member of built in pointer type does not `delete` the object to which that pointer points. 

### When is it called
#callingdestructor
- Variables are destroyed when they go out of scope
- Members of an object are destroyed when the object of which they are a part is destroyed
- Elements in a container - whether it's a library container or an array - are destroyed when the container is destroyed. 
- When we use `delete` to the dynamic allocated memory. 
- Temp objects are destroyed at the end of the full expression in which the temporary was created. 

This is why aggregation and composition are so important, so that we know when something is destructed or not. 
[[Aggregation]] - the existing class can still exist independently, this is with the hollow arrow. This is the "least offending one". 
[[Composition]] - the existing class is owned exclusively by the new class and cannot exist independently. This is shown with the filled in arrow. "Has a" relationship. 
![[Pasted image 20240412095239.png]]

```
{ // new scope
	// p and p2 point to dynamically allocated objects
	Sales_data *p = new Sales_data; // p is a built in pointer
	auto p2 = make_shared<Sales_data>(); // p2 is a shared pointer
	Sales_data item(*p); // copy constructor copies *p into item
	vector<Sales_data> vec; // local object
	vec.push_back(*p2); // copies the object to which p2 points 
	delete p; // destructor called on the object pointed to by p
} // exiting local space; destructor called on item, p2, and vec
// destroyed p2 decrements its use count, object is freed
// destroyed vec destroys the elements in vec
```

Our code directly frees only the dynamically allocated object bound to `p`. 

The other `Sales_data` objects are automatically destroyed when they go out of scope. 

`vector`, `shared_ptr` and `Sales_data` destructors all activate when `vec, p2, and item` go out of scope. 
`vector` destructor will delete all the elements we pushed onto it. 
`shared_ptr` will decrement the ref count, to which `p2` points to, which will delete the `Sales_data` object that `p2` allocated. 

`Sales_data` will implicitly destroy the `bookNo` member. 
This will, in turn, run the `string` destructor too, freeing the memory used to store the `ISBN`. 

### Synthesized Destructor
For any class that doesn't define its own destructor. 

The synthesized destructor for `Sales_data` will look something like this: 
```
class Sales_data{ 
public: 
	// no work to do other than destroying the members, which happens automatically
	~Sales_data() {}
	// other members as before
};
```
The members are automatically destroyed after the destructor body is run. 
The `string` destructor will be run to free the memory used by the `bookNo` member. 

Important to note that members are destroyed after the function body. 
This part is implicit. 

### The Rule of Three/Five
#rulethreefive
There are three basic operations to control copies of class objects: 
1. the copy constructor
2. copy-assignment operator
3. destructor

Now we'll have a look at how we define in a class, a move constructor and a move-assignment operator. 

There is no requirement that we define all these operations: We can define one or two of them without having to define all of them. 
These should all be thought of as a unit. 

**It is unusual to define one without needing to define them all**. 

#### Classes that use destructor, NEED COPY and ASSIGNMENT
#needfordestructors
Often the need for destructors is more obvious then the need for copy-control members. 
If a class needs a destructor, it almost definitely needs a copy constructor and copy-assignment operator as well. 

Let's say that we allocate dynamic memory in its constructor. 
The synthesized destructor will not `delete` a data member that is a pointer. Dynamic allocation can be tricky in this sense. 
Therefore, we would need to define a destructor to free that memory that we allocated. 

Having evaluated this, we also need to ascertain next that we definitely need a copy constructor, and a copy-assignment operator. 
This should be fairly clear. 

Consider what might happen here, if we have a destructor, yet not a copy-constructor and copy-assignment operator. 

```
class HasPtr{ 
public: 
	HasPtr(const std::string &s = std::string()); ps(new std::string(s)), i(0) {]}

	~HasPtr() { delete ps;}

	// WE NEED A copy constructor and copy-assignment operator
	// other members will be as before
};
```

We have introduced a serious bug here. 
Those synthesized copy and copy assignment here will copy the pointer member, meaning that multiple `HasPtr` objects may be pointing to the same memory:
```
HasPtr f(HasPtr hp) // HasPtr passed by value, so it is copied
{ 
	HasPtr ret = hp; // copies the given HasPtr
	// process ret
	return ret; // ret and hp are destroyed
}
// returning by value, meaning that ret and hp are indeed destroyed
```
When `f` return both `hp` and `ret` are destroyed and `HasPtr` destructor is run on each of these objects. 
The `destructor` here will delete the pointer member in `ret` and `hp`. They contain the same pointer value. 
This code will `delete` that pointer twice, which is an error. What happens then is undefined. 


In addition, the caller of `f` may still be using the object that was passed to `f`: 
```
HasPtr p("some values");
f(p); // when f completes, the memory to which p.ps points is freed
HasPtr q(p); // now both p and q point to invalid memory
```

### CLASSES THAT NEED COPY, NEED ASSIGNMENT AND VICE VERSA
This is really because we can move a bunch of objects in a bunch of ways and we need to cover our bases. 

We may have no need for destructing, however, copy and copy assignment may need to be defined here. 

Consider a class, each object has its won unique serial number. 
Such a class would need a copy constructor to generate a new (uniqueness concept here), distinct serial number for the object being created. 
We also need our own copy-assignment operator avoid assigning to the serial number of the left hand object. 

Just a quick note of the differences between the two here: 
#copyconstructor #copyassignment
Copy constructor is used to create a new object as a copy of an existing object. Called when a new object is initialized with an existing object. Takes reference to an object of the same class as a parameter. 
```
MyClass(const MyClass& other){ 
}
```
You can tell this will be the case when we create a new object from another object. 


Copy assignment operator is used to copy the contents of an existing object into another existing object. Takes a reference to an object of the same class as a parameter, much like `const MyClass& other` in copy constructor. 
```
MyClass& operator=(const MyClass& other){ 
	// copy assignment operator implementation
	return *this;
}
```
Notice the return type here!
![[Pasted image 20240412111751.png]]
Copy constructor would be used in this case, as we are creating a new object: 
```
MyClass Obj1; 
MyClass Obj2 = Obj1; 

MyClass someFunction(){ 
	MyClass obj; 
	return obj; 
	// copy constructor invoked when returning obj by value
}
```

Copy assignment invoked in cases like this: 
```
MyClass obj1; 
MyClass obj2; 
obj2 = obj1; 
```


If a class needs a copy constructor, it almost surely needs a copy-assignment operator. 
A vice versa - if the class needs an assignment operator, it almost always needs a copy constructor as well. 
This gives rise to a second rule of thumb: **if we need a copy constructor, we almost always need a copy-assignment operator** and vice versa. 
However, having both, does **not mean that we have to have a destructor as well**. 

### using `= default;`
We can explicitly ask for the compiler to generate a synthesized version of the copy control members by defining them as `=default`. 
```
class Sales_data{ 
public: 
	// copy control; use defaults
	Sales_data() = default;
	Sales_data(const Sales_data&) = default;
	Sales_data& operator=(Const Sales_data&);
	~Sales_data() = default;
};

Sales_data& Sales_data::operator=(const Sales_data&) = default;
```

When using `= default` on the declaration of the member inside the class body, the synthesized function is implicitly inline (just as any other member function defined in the body of the class). 

We can use `= default` on member functions that have a synthesized version (ie. the default constructor or a copy control member). 

### Preventing Copies
Sometimes we have to define in order for a class to not create copies. 
eg. the `iostream` class does this, preventing making copies or assignments begin made. 
This is so that we don't have multiple objects reading or writing from the same IO buffer. 
Remember that our issue here will be that of synthesized copy constructors etc. so just not defining them makes it an issue. 

### Defining a Function as `Deleted`;
We can prevent copies by defining the copy constructor and copy-assignment operator as **deleted function**. 
A deleted function is declared, but may not be used in any other way. 

We just use the `= delete;` idea. 

```
struct NoCopy{ 
	NoCopy() = default; // use synthesized default constructor
	NoCopy(const NoCopy&) = delete;
	NoCopy operator=(const NoCopy&) = delete; // no assignment
	~NoCopy() = default; // synthesized destructor
	// other members
};
```

`delete` will signify that we are intentionally not defining these members. 

Unlike `= default`, `= delete` will have to be used on the first declaration of a deleted function, we can't declare, and then `delete` outside the class/struct. 

A default member affects only what the compiler generates; hence the `= default` is not needed until the compiler generates code. 
However, a compiler will need to know that a type of function is deleted before we even begin to generate code for it, as something might use it later in our code and we don't want that, that's the whole point. 

Also, we can specify `= delete` on any function, although the main use for suppressing copy-control functions, sometimes it's also useful when we want to guide the function-matching process. 

### Destructor SHOULD NOT BE DELETED
The compiler will let you know that you can't create variables of a type that doesn't have a destructor. 
If a member has a deleted destructor, then we simply can't delete it. 
We are still able to dynamically allocate them, however, we wouldn't be able to free them. 

```
struct NoDtor{ 
	NoDtor() = default; 
	~NoDtor() = delete; // can't destroy objects of this type
};

NoDtr nd; // Error;
NoDtr *p = new NoDtor();
delete p; // error no destructor available. 
```

#### The Copy-Control Members May be Synthesized as Deleted
This is the idea that synthesized members may be deleted by the compiler: 
- The synthesized destructor is defined as deleted if the class has a member whose own destructor is deleted or is inaccessible. 
- The synthesized copy constructor is defined as deleted if the class has a member whose own copy constructor is deleted or inaccessible. Also deleted if the class has a member with a delete or inaccessible destructor. 
- The synthesized copy-assignment operator is defined as deleted if a member has a deleted or inaccessible copy-assignment operator,  or if the class has  `const` or reference member. 
- The synthesized default constructor is defined as deleted if the class has a member with a deleted or inaccessible destructor: or has a reference member that does not in-class initializer; or has a `const` member whose type does not explicitly define a default constructor and that member does not have an in-class initializer. 
Mostly when a member, who's of another type, doesn't have a synthesized `x`. 

In essence, this means that if a class has a data member that cannot be default constructed, copied, assigned, or destroyed, then the corresponding member will be a deleted function.

If we cannot destroy, then if we could copy, then we will struggle to destroy objects there too. 

The compiler will not synthesize a default constructor for a class with a reference member or a `cosnt` that cannot be default constructed. So essentially, can't default construct, therefore, can't synthesize one for you. The default `const` value will be that forever. 
There are other reasons too, that we will get to later on. 

[[Copy Control]]




My Example: 


```class Test{
public:
    Test(string a) : name(a) {}
	
    Test() : Test("default") {}

    ~Test() { printf("Deleting\n");}
	
    void PrintTest() { cout << name << endl;}

    Test(const Test& other){
        cout << "Copy Constructor Called\n";
        name = other.name;
    }

    string name;
};

Test retTest(const Test& t){
    cout << "RetTest called: " << t.name << "\n";
    return t;
}


int main(){
    Test ii("Hugo");
    auto f = retTest(ii);
    cout << f.name << endl; // output "Hugo"
```
Here, if our copy constructor was: 

```
Test(const Test& other){ 
	cout << "Copy Constructor called\n";
}
```

Then we will not be copying the name, our constructor doesn't do that, the synthesized one might, however ours does not here. 
Therefore when we do `cout << f.name << endl;` our output wouldn't be `"Hugo"`, it would be nothing, our copy constructor says that we don't copy the name. 