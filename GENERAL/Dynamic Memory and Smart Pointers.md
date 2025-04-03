
[[Dynamic Memory Overview]]
#smartpointers


This memory is managed through a pair of operators: `new`, returning a pointer to that object , 
and then `delete`, which takes a pointer to a dynamic object, destroys that object, and frees the associate memory. 

It is weirdly hard to ensure that we free memory at the right time. 
Either we forget to free it, or we have a memory leak - or we free the memory when there are still pointers referring to that memory - dangling pointers. 

Two smart pointers, which is like a regular pointer with the important exception that it automatically deletes the object to which it points. 
There are two types: `shared_ptr`, allowing for multiple pointers to refer to the same object, and a `unique_ptr` which owns the object to which it points. 
The library also defines a companion class named `weak_ptr` that is a weak reference to an object managed by `shared_ptr`. They are all defined in the `memory` header. 


## `shared_ptr` Class
Like vectors, smart pointers are templates. 
Therefore, when we create one, we have to supply additional information - in this case, the type to which the pointer can point. 
As with `vector`, we supply that type inside angle brackets that follow the name of the kind of smart pointer we are defining: 
```
shared_ptr<string> p1; // shared_ptr that can point at a string
shared_ptr<list<int>> p2; // shared_ptr that can point at a list of ints
```

A default initialized smart pointer will hold a null pointer. 
We use a smart pointer in ways that are similar to using a pointer. 
Dereferencing a smart pointer returns the object to which the pointer points. 
Using one in a condition, is to test whether the pointer is null: 
```
// if p1 is not null, check whether it's the empty string
if(p1 && p1->empty()) // if p1 exists and the object is not empty string
	*p1 = "hi"; // if so, dereference p1 to assign a new value to that string 
```

This table shows the operations that are common to `shared_ptr` and `unique_ptr`. 
The second one is just `shared_ptr`s. 
![[Pasted image 20240401213841.png]]

### The `make_shared` Function
#make_shared
The safest way to allocate and use dynamic memory is to call a library function named `make_shared`. 
It will allocate and initialize an object in dynamic memory and returns a `shared_ptr` that points to the object. 
Like the smart pointers, `make_shared` is defined in the `memory` header. 

When we call it, we must specify the type of object we want to create. 
We do so in the same way as we use a template class, by following the function name with a type enclosed in brackets: 
```
// shared_ptr that points to an int with value 42
shared_ptr<int> p3 = make_shared<int>(42);

// p4 points to a string with value 99999
shared_ptr<string> p4 = make_shared<string>(10, '9');

// p5 points to an int that is initialized to 0
shared_ptr<int> p5 = make_shared<int>();
```

Remember how with sequential containers, `emplace` will create an object and then add it to the list, `make_share` uses its arguments to construct an object of the given type. 
eg. `make_shared<string>` must pass argument(s) that match one of the `string` constructors. 
Calls to `make_shared<int>` can pass any value we can use to initialize an `int`. 
If we do not pass any arguments, then the value will be default initialized. 

Of course, ordinarily we use `auto`, to make it easier to define an object to hold the result of `make_shared`: 
```
// p6 points to a dynamically allocated, empty vector<string>
auto p6 = make_shared<vector<string>>();
```
The basics is that we create an object, and then returned a shared pointer to it. 

### Copying and Assigning `shared_ptr`s
When we copy or assign a `shared_ptr`, each `shared_ptr` keeps track of how many other `shared_ptr`s there are to the same object: 
```
auto p = make_shared<int>(42); // object to which p points has one user
auto q(p); // p and q point to the same object
	// object to which p and q point has two users
```


We can think of `shared_ptr` as if it has an associated counter, known as the **reference count** #referencecount

When we copy a `shared_ptr`, the count is incremented. 

The counter associated with a `shared_ptr` is incremented when we : 
- when we use that `shared_ptr` to initialize another `shared_ptr`
- when we use it as a right hand operand of an assignment 
- when we pass it or return it from a function by value.

The counter will be decremented when we assign a new value to the `shared_ptr` and when the `shared_ptr` itself is destroyed, such as when a local `shared_ptr` goes out of scope. 

One a `shared_ptr`'s counter goes down to 0, the pointer will automatically free the object that it manages: 
```
auto r = make_shared<int>(42); // int to which r points has one user
r = q; // assign to r, making it point to a different address
// increase the use count for the object to which q points
// reduce the use count of the object which r had pointed
// the object r had pointed to has no users, that object is automatically freed
```
 Here we allocate an `int` and store a pointer to that `int` in r. 
 We assign a new value to `r`. 
 `r` was the only `shared_ptr` pointing to the one we previously allocated. 
 The `int` is automatically freed as part of assigning `q` to `r`. 
It is up to the implementation whether to use a counter or another data structure to keep track of how many pointers share state. 
The key point is that the class keeps track too, of how many `shared_ptr`s point to the same object and automatically frees that object when appropriate and when we have no pointers pointing to it. 

#### `shared_ptr`s Automatically Destroy Their Objects.... 
When the last shared pointer is destroyed, the `shared_ptr` class automatically destroys the object to which that `shared_ptr` points. 
It does this through another special member, the destructor. 
Analogous to its constructors, each class will have a destructor. 
Just as a constructor controls the initialization, the destructor controls what happens when objects of that class type are destroyed. 

eg. `string` constructors (and other `string` members) allocate memory to hold the characters that compose the `string`. 
The `string` destructor frees that memory. 
Similarly, several `vector` operations allocate memory to hold the elements in the `vector`. 
The destructor for it will destroy those elements and frees the memory used for the elements. 

For `shared_ptr`s, when the count goes down to 0, the destructor destroys the object to which the `shared_ptr` points and frees the memory used by that object. 

#### and Automatically Free the Associated Memory
```
// factory returns a shared_ptr poitning to a dynamically allocated object
shared_ptr<Foo> factory(T arg)
{ 
	// process arg as appropriate
	// shared_ptr will take care of deleting this memory
	return make_shared<Foo>(arg);
}
```

As this function is returning a `shared_ptr`, we can be sure that the object allocated by `factory` will be freed when appropriate. 
Then we assign that returned `shared_ptr` to another `shared_ptr`: 
```
void use_factory(T arg) // we are copying the object Foo arg here
{ 
	shared_ptr<Foo> p = factory(arg);
	// use p here
}// p goes out of scope; the memory to which p points is automatically freed
```
As `p` is local to `use_factory`, it is destroyed when `use_factory` ends. 
When `p` is destroyed, its reference count is decremented and checked. 
In this case, `p` is the only object referring to the memory returned by `factory`. 
However, the memory will not be freed if there is another `shared_ptr` to `p`. 

```
shared_ptr<Foo> use_factory(T arg)
{ 
	shared_ptr<Foo> p = factory(arg);
	// use p
	return p; // reference count is increemented when we return p
} // p goes out of scope; the memory to which p points is not freed
```

The return statement here in `use_factory` returns a copy of p to its caller. 
Copying a `shared_ptr` adds to the reference count of that object. 

Now when `p` is destroyed, there will be another user for the memory to which `p` points. 
The `shared_ptr` class ensures that so long as there are any `shared_ptr`s  attached to that memory, the memory itself will **not be freed**. 
As long as we can reference that object, we cannot delete that object. 


The thing to remember here is that if we have a floating `shared_ptr`, we might not realise that that memory has not been deleted. If we don't know that there is a `shared_ptr` somewhere, then we might believe that we are deleting an object, when really we are not. 

If you put `shared_ptr`s into a container, and you subsequently need to use some, but not all, of the elements, remember to `erase` the elements you no longer need. 

#### Classes with Resources that Have Dynamic Lifetime
Programs tend to use dynamic memory for one of three purposes: 
1. They don't know how many objects they'll need
2. They don't know the precise type of the objects they need
3. They want to share data between several objects.

Container classes are good examples of classes that use dynamic memory for the first purpose and we'll see more later on. 
Here we'll define a class that uses dynamic memory, in order to let several objects share the same underlying data. 

So far, the classes we've used allocate resources that exist only as long as the corresponding object does. 
`vector` s own their elements. When we copy a vector, the elements in the original `vector` and in the copy are separate in one another. 

```
vector<string> v1; // empty vector
{// new scope 
	vector<string> v2 = {"a", "an", "the"};
	v1 = v2; // copies the elements from v2 into v1
}
// v2 is destroyed, which destroys the elements in v2
// v1 has three elements, which are copies of the ones originally in v2. 
```

When the vector is destroyed, all of the elements will be destroyed as well. 

Some classes will allocate resources with a lifetime that is independent of the original object. 
As an example, assume we want to define a class named `Blob` that will hold a collection of elements. 
Unlike containers, we want `Blob` objects that are copies of one another to share the same elements. 
When we copy a `Blob`, the original and the copy should refer to the same underlying elements. 
Essentially they refer to the same element. 

When they share the same data, we cannot destroy the underlying data when one of the objects is destroyed, `shared_ptr` s are goated here. 
```
Blob<string> b1; // empty Blob
{ // new scope 
	Blob<string> b2 = {"a", "an", "the"};
	b1 = b2; // b1 and b2 now share the same elements
} // b2 will be destroyed here, the elements cannot be destroyed though
// b1 points to the elements that are originally created by b2
```

 One common usage of dynamic memory is that it allows multiple objects to share the same state. 

##### Defining the `strBlob` class
Ultimately we'll define this as a template class, which we will also learn about later. 
[[BtB Templates]] this covers a large amount of what we'll be doing anyways. 

The easiest way to implement a new collection type is to use one of the library containers to manage the elements. 
We can let the library type manage the way that the elements are stored. 
 We'll use a vector here, but could use something else if we wanted to. 

We can't store the objects directly in the vector, as when the vector is destroyed, then the elements will be too, we need the vector to hold the pointers to the elements. 
Therefore, we can just store the vector itself in dynamic memory. 
The implement the sharing, we'll give each `strBlob` a `shared_ptr` to a dynamically allocated `vector`. 

When the last `strBlob` has been destroyed, then that shared vector will be destroyed as well. 
We will have a default constructor and a constructor that has a parameter of type `initializer_list<string>` taking a braced list of initializers. 

```
class StrBlob{ 
public: 
	typedef std::vector<std::string>::size_type size_type;
	StrBlob(); 
	StrBlob(std::initializer_list<std::string> il);
	size_type size() const { return data->size();}
	bool empty() const { return data->empty();}
	// add and remove elements
	void push_back(cont std::string &t) { data->push_back(t);}
	void pop_back();
	// element access
	std::string& front();
	std::string& back();

private: 
	std::shared_ptr<std::vector<std::string>> data;
	// thorws msg if data[i] isn't valid
	void check(size_type i, const std::string& msg) const;
};
```
Calling `size_type size() const { return data->size(); }`
We have to use the dereferencer here. 
Data is a shared pointer to a vector of strings called `data`. 

### `StrBlob` Constructors
Each constructor uses its constructor initializes list to initialize its `data` member to point to a dynamically allocated `vector`. 
The default constructor allocates an empty `vector`. 
```
StrBlob::StrBlob() : data(make_shared<vector<string>>()) {} // creating a dyanimcally allocated vector using a shared_ptr

StrBlob::StrBlob(initializer_list<string> il) : data(make_shared<vector<string>>(il)) {}
```

The constructor that takes an initializer list, passes its parameter to the corresponding `vector` constructor. 
It initializes the `vector`'s elements by copying the values in the list. 

##### Element Access Members
The `pop_back, front, and back` operations access members in the `vector`. 
These must check that the element exists before attempting to access that element. 
Several members need to do the same checking, we've given our class a `private` utility function named `check` that verifies that a given index is in range. 
In addition to an index, `check` takes a `string` argument that it will pass to the exception handler. 
The `string` describes what went wrong. 
```
void StrBlob::check(size_type i, const string& msg) const { 
	if( i >= data->size())
		throw out_of_range(msg);
}
```

So we would need to check every time that we want to access members. 
```
string& StrBlob::front(){ 
	// if the vector is empty, check will throw
	check(0, "front on empty StrBlob");
	return data->front();
}

string& StrBlob::back(){ 
	check(0, "back on empty StrBlob");
	return data->back();
}

void StrBlob::pop_back()
{ 
	check(0, "pop_back on empty StrBlob"); 
	data->pop_back();
}
```

The `front` `back` members should be overloaded on `const`. Defining those versions is left as an exercise. 


### Copying, Assigning, and Destroying `StrBlob`s
`StrBlob` uses the default versions of the operations that copy, assign and destroy objects of its type. 
As we've seen, copying a `shared_ptr` increments its reference count; 
assigning one `shared_ptr` to another increments the count of the right-hand operand and decrements the count in the left hand operand; and destroying a `shared_ptr` decrements the count. 
If the count in a `shared_ptr` goes to zero, the object to which that `shared_ptr` points is automatically destroyed. 
The `vector` allocated by the `StrBlob` constructors will be automatically destroyed when the last `StrBlob` pointing to that `vector` is destroyed. 




[Tour of Smart Pointers](https://isocpp.org/files/papers/5-Tour-Util.pdf)

```
void f(int i, int j)
{ 
	X* p = new X; // allocate a new X 
	unique_ptr<X> sp{new X};
	// allocate a new X and give its pointer to unique_ptr

	if (i<99) throw Z{}; // may throw an exception
	if (j<77) return; // may return early

	p->do_something(); // may throw an exception
	sp->do_something(); // may throw an exception
	// ...

	delete p; // destroy *p
}
```

We don't delete `p` here is `i<99` or if `j<77`. 
The `unique_ptr` ensures its objects is properly destroyed whichever way we exit `f()`. 

Of course you could just do this 
```
void f(int i, int j)// using a local variable
{ 
	X x;
	// ...
}
```

We might also do this: 
```
unique_ptr<X> make_X(int i)
	// make an X and immediately give it to a unique_ptr
{ 
	// check i, etc.
	return unique_ptr<X>{new X{i}};
}
```

Like a `vector` is a handle to a sequence of objects. They both control the lifetime of other objects (**using RAII**) and both rely on move semantics to make `return` simple and efficient. 

`shared_ptr`s are copied rather than moved. 
```
void f(shared_ptr<fstream>);
void g(shared_ptr<fstream>);
void h(shared_ptr<fstream>);

void user(coinst string& name, ios_base::openmode mode)
{ 
	shared_ptr<fstream> fp{new fstream(name, mode)};
	if(!*fp) throw No_file{}; // make sure the file was opened properly


	f(fp);
	g(fp);
	h(fp);
	//... 
}
```

`f,g,h` may have a copy that outlives `fp`. 

When referring to a polymorphic object, we need a pointer, because we don't know the exact type of the object or even its size. 

You **don't** to use a pointer to return a collection of objects from a function ; a container that is resource handle will do that simply and efficiently. 