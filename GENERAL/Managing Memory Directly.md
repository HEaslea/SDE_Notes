C++ defines two operators that allocated and free dynamic memory. 
The `new` operator allocates memory, and `delete` frees memory allocated by `new`. 
These are far more error prone than using smart pointers. 

Smart pointers mean that programs are debuggable and easier to read etc, and probably understand. 

#### Using `new` to Dynamically Allocate and Initialize Objects
#usingnew
Objects on the free store remained unnamed, so `new` offers no way to name the objects that it allocates, they don't really need to be named, as the pointers to them can be named and that's what matters and how we can differentiate. 

`new` returns a pointer to the object it allocates: 
```
int *pi = new int; // pi points to a dynamically allocated
	// unnamed, uninitialized int
```

This `new` expression constructs an object of type `int` on free store and returns a pointer to that object. 

By default, they are default initialized, which means that objects of built-in or compound type have undefined value; objects of class type are initialized by their default constructor: 
```
string *ps = new string; // intiailized to empty string
int *pi = new int; // pi points to an uninitialized int
```

To initialize to a value, we use the traditional construction, and under the new standard, we can also use list initialization (with curly braces): 
```
int *pi = new int(1024); // object to which pi points has value 1024
string *ps = new string(10,'9'); // *ps is "9999999999"

// vector with ten elements with values from 0 to 9
vector<int>* pv = new vector<int>{0,1,2,3,4,5,6,7,8,9};
```

```
string *ps1 = new string; // default initialized to the empty string
string *ps = new string(); // value initialzied to the empty string

int *pi1 = new int; // default intiailized; *pi1 is undefined
int *pi2 = new int(); // value intialized to 0;  
```

For class types (such as `string`) that define their own constructors, requesting value initialization is of no consequence; regardless of form, the object is initialized by the default constructor. 
default value-initialized of built-in types has a well-defined value, however the default-initialized object does not. 
Members of a built-in type in classes that rely on the synthesized default constructor will also be uninitialized if those members are not initialized in the class body (the compiler created default constructor). 

For the same reasons as we usually initialize variables, it is also a good idea to initialize dynamically allocated objects, to avoid undefined behaviours. 

We can just use auto to get the pointer type to that object: 
```
auto p1 = new auto(obj);
// p points to an object of the type of obj
// that object is initialized from obj class

auto p2 = new auto{a, b, c}; // error must use parentheses for the initializer
```
The type of `p1` is the pointer to the auto - deduced type of `obj`. 
If `obj` is an `int`, then p1 is an `int*` etc. 

### Dynamically Allocated  `const` Objects
#dynamicallyallocatedconst
```
// allocate and initialize a const int
const int* pci = new const int(1024);
// allocate a default-initialized const empty string
const string* pcs = new const string;
```

Like other `const` they must be initializer when dynamically allocated as well. 
A `const` dynamic object of a class type that defines a default constructor may be initialized implicitly. 
The pointer returned is a pointer to `const` DUH. 

### Memory Exhaustion
#memoryexhaustion
Although modern machines have a lot of memory, it is alwasy possible that the free store will be exhauste . 
Once a program has used all of its available memory, `new` expressions will fail. 
If `new` is unable to create and allocate storage, it throws an `exception` of type `bad_alloc`. 
We can prevent `new` from throwing an exception by using a different form of `new`: 
```
// if allocation fails, new returns a null pointer
int *p1 = new int; // throws exception of bad_alloc
int *p2 = new(nothrow)int; // if allocation fails, new returns a null pointer
```

#placementnew
We'll see later that this is known as **placement new**. 
We pass the object named `nothrow` that is defined by the library. 
Telling `new` not to throw an exception. 
If this form of `new` is unable to allocate the requested storage, it will return a null pointer. 

```
size_t size = 10;
int *arr  = new int[size];

if(arr == nullptr){ 
	cout << "Mem Allocation Failed" << endl;
	return 1;
}

for(int i = 0; i < size; ++i){ 
	arr[i] = i; // remember that subscripting arrays uses the equation of
	// *(arr + i) where arr is the pointer to the first element
}

for(int x = 0; x < size; ++x){ 
	cout << arr[x] << " : ";
}
cout << endl;

delete[] arr; // deleting the arr
```

### Freeing Dynamic Memory
#freeingdynamicmemory
So that we don't prevent memory exhaustion, we must return dynamically allocated memory to system once we are finished using it. 
`delete **expression**` 
A `delete` expression takes a pointer to the object we want to free: 

`delete p; // p must point to a dynamically allocated object or be null`

**It does two actions**: 1. destroying the object to which its given pointer points (0'ing that object) 2. frees the corresponding memory

### Pointer Values and `delete`
It must be either a dynamically allocated memory or a null pointer. 
Deleting a pointer to memory that was not allocate by `new`, or deleting the same pointer value more than once undefined: 
```
int i, *pi1 = &i, *pi2 = nullptr; 
double *pd = new double(33), *pd2 = pd;
delete i; // error i is not a pointer
delete pi1; // pi1 refers to local memory
delet pd; // fine 
delete pd2; // the memory here is already freed
delete pi2; // ok, fine to delete a null ptr
```

In general, compilers will not be able to tell whether a pointe rpoints to a statically or dynamically allocated object. 
The compiler also cannot tell whether memory addressed by a pointer has already been freed. 
Most compilers will accept these `delete` expressions, even though they are in error, which is annoying but fair. 
Although the value of `const` objects cannot be modified, the object itself can be destroyed. 
It's just the same as above: 
```
const int *pci = new const int(1024);
delete pci; // ok deletes a const object
```

##### Dynamically Allocated Objects Exist Until they Are Freed
`shared_ptr`s allow us the luxury of deleting the object when the last pointer goes out of scope. However, if we allocate ourselves, this is not true, the objects will still exist until explicitly deleted. 

Functions returning pointers (rather than smart pointers) to dynamic memory put a burden on their callers - the caller must remember to delete the memory: 
```
// factory returns a pointer to a dynamically allocated object
Foo* factory (T arg){ 
	// process arg as appropriate
	return new Foo(arg); // caller is responsible for deleting this memory
}
```

So then if we did something like this: 
```
void use_factory(T arg)
{ 
	Foo *p = factory(arg);
	// use p but do not delete it
} // p goes out of scope, but the memory to which p points is not freed!
```

If this was a `shared_ptr` then it would do it for us, however, it's not :(. 

When objects of built-in type are destroyed, nothing really happens. 
When a pointer goes out of scope, nothing happens to the object to which the pointer points. 
The fixed version would be: 
```
void use_factor(T arg)
{ 
	Foo* p = factor(arg);
	// use p
	delete p; // remember to free the memory now that we are no longer using it
}
```

> **Caution**: Managing Dynamic Memory is Error-Prone: 
> 1. Forgetting to delete memory; this is known as a memory leak, as the memory is never returned to the free store. It's difficult to find these leaks, because they are usually not detectable until the application is run for a long enough time to actually exhaust memory. 
> 2. Using an object after it has been deleted, don't you hate it when this happens: this can be detected by making the pointer null after the delete. 
> 3. Deleting the same memory twice. This can happen when two pointers address the same dynamically allocate object. If `delete` is applied to one of the pointers, then the object's memory is returned to the free store. If we subsequently `delete` the second pointer, then the free store may be corrupted. 

They are so much easier to make than to fix and find in the code. 

You can avoid all of these (lol) by using **smart pointers** exclusively. 
The smart pointer will take care of deleting the memory **only** when there are no remaining smart pointers pointing to that memory. 

##### Resetting the Value of a pointer after a `delete`
#danglingpointers
Many machines allow that pointer to exist, and to hold the address of the freed memory. 
A dangling pointer is one that refers to memory that once held an object but no longer does so. 

We can avoid things like this by dangling pointers by deleting the memory associated with a pointer just before the pointer itself goes out of scope. 
Then there is no way or change the pointer after the memory associated with the pointer is freed. 
If we need to keep the pointer around for whatever reason, we can assig a `nullptr` to the pointer after we use `delete`. 
Doing so makes it clear that the pointer points to no object. 

However this is only limited protection: 
There could be several pointers pointing to the same memory. 
Resetting the pointer we use to `delete`will have no effect on the other pointers. 
Other pointers will point to the (freed) memory: 
```
int* p(new int(42)); // p points to dynamic memory
auto q = p; // p and q point to the same memory
delete p; // invalides both p and q
p = nullptr; // indicates that p is no longer bound to an object
```
They `p and q` are both pointing to the same dynamically allocated object. 
We `delete` that memory and set p to `nullptr`, indicating that the pointer no longer points o an object. 
Resetting p has no effect on q, which became invalid when we deleted the memory to which p (and q!). 
Finding all the memory is surprisingly difficult. 

#### `shared_ptr` with `new`
#newandshared

If we do not initialize a smart pointer, it will be initialized as a null pointer. 
We can also initialize a smart pointer from a pointer returned by `new`. 
```
shared_ptr<double> p1; // shared_ptr that can point at a double
shared_ptr<int> p2(new int(42)); // p2 points to an int with value 42
```
![[Pasted image 20240403143620.png]]The smart pointer constructors that take pointers are `explicit`. Hence we cannot implicitly convert a built-in pointer to a smart pointer; we must use the direct form of initialization to initialize a smart pointer. 

```
shared_ptr<int> p1 = new int(1024); // ERROR; must use direct initialization

shared_ptr<int> p2(new int(1024)); // Ok uses direct initialization
```

`p1` is asking the compiler to create a `shared_ptr` out of the `int*` from `new`. 
It can't implicitly convert a pointer to a smart pointer, this is an error and is erroneous and is wrong lol.
For the same reason, a function that returns a `shared_ptr` cannot implicitly convert a plain pointer in its return statement. 

Just remember that we cannot implicitly convert between smart pointers and normal pointers. 
```
shared_ptr<int> clone(int p){ 
	return new int(p); // error implicit conversion to shared_ptr<int>
}
```

We must explicitly bind a `shared_ptr` to the pointer we want to return: 
```
shared_ptr<int> clone(int p){ 
	// this is fine, direct initialize from a pointer
	return shared_ptr<int>(new int(p));
}
```

By default, the pointer that we use here must point to the dynamic memory (the free store, the heap), because, by default, smart pointers will use `delete` in order to free the memory, this wouldn't work for pointers pointing to the stack or static memory. 

We can bind smart pointers to pointers to other kinds of resources. 
However to do so, we must supply our own operation to use in place of `delete`. 
We'll see this in a bit. 


## DO NOT MIX ORDINARY POINTERS AND SMART POINTERS
#donotmixordinarypointersandsharedpointer
[[DO NOT MIX ORDINARY POINTERS AND SHARED POINTERS]]

`shared_ptr` s can only coordinate with other `shared_ptr`s that are copies of itself. 
`make_shared` is our friend as we bind a `shared_ptr` to the object the same time that we allocate it. 

Consider this : 
```
// ptr is created and initialized when process is called 
void process(shared_ptr<int> ptr)
{ 
	// use ptr
}// ptr goes out of scope and is destroyed
```

Remember that with `make_shared`, it is just a function that returns a `shared_ptr`. Meaning that we do have to assign it. 
`auto p = make_shared<int>(10);`
It's a template function remember. 

```
template<typename T>
void fTest(shared_ptr<T> p){
    printf("fTest\n");
}

int main(){
    auto p = make_shared<Test>(1, "Hugo");
    cin.get();
    fTest(p);
    cin.get();
```

Here, when we pass into `fTest`, we are creating a copy of the `shared_ptr`, it is still pointing to the same object, however, we are creating a copy, so when we leave that scope of the function we are not destroying the last `shared_ptr` and the object. 

Even if we pass by reference, it doesn't change the ownership semantics of the `shared_ptr` to the function. The ownership is still managed by the `shared_ptr` outside the function. 

If you were to do something like this: 
```
void foo(const boostd::shared_ptr<Pfoo>& rx){ 
	myvector->push_back(rx);
}
```
Here, when we pass by reference, we are not incrementing the reference count, however, when we `push_back(rx)`, then we are copying and incrementing the reference count. 

Copying `shared_ptr`s, as we know, increments the reference count. Thus, inside `process` the count is at least 2. 
When we leave the function, the ref count will be decremented by 1, but can't go down to 0 .
The right way to do this function would be something like this: 
```
share_ptr<int>p(new int(42)); // reference count is 1
process(p); // copying p increments the count; in process the count is 2
int i = *p; // ok ; reference count is 1
```

Although we cannot pass  a built-in pointer to `process`, we can pass `process` a temporary object, `shared_ptr` that we explicitly construct from a built-in pointer. 

```
int* x(new int(1024)); // dangerous ; x is a plain pointer, not a smart pointer
process(x); // error as we cannot convert an int* to shard_ptr<int>
process(shared_ptr<int>(x)); // legal, but the memory will be deleted
int j = *x; // undefined; x is a danling pointer
```
The base rule here is that we just don't mix the two together. 
Here we passed a temporary `shared_ptr` to `process`. 

For `shared_ptr`s there is the idea of a control block, an internal data structure, to manage the shared ownership of the dynamically allocated object. 
Typically containing a pointer to the object, a reference count to track the number of `shared_ptr` s and some other memory managements. 

When we create a new `shared_ptr` from a raw pointer, using the constructor that we have above, then we create a new control block, managing the shared ownership of that object pointed to by the raw pointer. 
**This control block is separate to other control blocks about the same object in memory**. 
It does this for safety reasons, and to maintain the integrity of reference counting, that's why we tend not to mix them, it makes things very awkward. 

There is also the idea here of **ownership semantics** as there was **move semantics**. 

Here with our example, `x` is still pointing to this memory, however the smart pointer will have freed that memory. 
Therefore `x` will be a dangling pointer. 

Attempting to use the value of `x` is undefined. 
When we bind a `shared_ptr` to a plain pointer, we give responsibility for that memory to the `shared_ptr`. 
We have to scrap that built-in pointer, so that we have better defined memory usage. 

### DONT USE GET TO INITIALIZEO OR ASSIGN ANOTHER SMART POINTER

The smart pointer types define a function named `get`. 
It returns a built-in pointer to the object that the smart pointer is managing. 
This function is intended for cases when wee need to pass a built-in pointer to code that can't use a smart poitner. 
The code using `get` must not `delete` that pointer. 

Although the compiler will not say; it is an error to bind another smart pointer to the pointer returned by `get`: 
```
shared_ptr<int> p(new int(42)); // ref coutn is 1
int *q = p.get(); // ok; just dont' use q in any way that might deleet its pointer
{// new block 
	// undefined; two independent shared_ptrs point to the same memory
	auto local = shared_ptr<int>(q);
} // block ends, local is destroyed, and the memory to which both p and q point is freed
int foo = *p; // undefined; the memory was freed
```

The overall idea is that we shouldn't really use a built-in pointer to initialize a `shared_ptr` as they take ownership, and they create a new control block that doesn't have another reference to any other `shared_ptr` to that memory. 
When it goes out of scope, this new `shared_ptr`, not in reference to another perhaps, might delete that memory associated with it, however, the other `shared_ptr` doesn't know this, and you might want to use it. 

So the example up above is a perfect case of not what to do. 

> Use `get` only to pass access to the pointer to code that you know will not delete the pointer. Never use `get` to initialize or assign to another smart pointer. 

### Other `shared_ptr` Operations
#sharedptroperations
```
p = new int(1024); // error cannot assign a pointer to a shared_ptr
p.reset(new int(1024)); // ok p points to new object
```

`reset` updates the reference counts, and if appropriate, deletes the object to which p points. 
The `reset` member is often used together with `unique` to control changes to the object shared among several `shared_ptr`s. 
Before changing the underlying object, we check whether we're the only use. 
If not, we make a new copy before making the change. 
```
if(!p.uniqe())
	p.reset(new string(*p)); // we aren't alone, allocate a new copy
*p += newVal; // now that we know we're the only pointer, okay to change this object. 
```
