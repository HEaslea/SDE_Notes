[[Exceptions And Throwing Them]] [[Exception Handling]] [[Dynamic Memory and Smart Pointers]]
#smartpointersandexceptions

If we throw exceptions, preventing our program from fully crashing if we have an error. We have to fortune tell what kind of issue we might have in the future, or how might something fail etc. 

One easy to make sure resources are freed is to use smart pointers. 
If we have exceptions that are thrown, we might not have freed some resources. 

```
void f()
{ 
	shared_ptr<int> sp(new int(42)); // allocate a new object
	// code that throws an exception that is not caught inside f
} // shared_ptr freed automatically when the function ends
```
When a function is exited, whether through normal processing or due to an exception, all the local objects are destroyed,. 
Destroying `sp` here will look at the reference count of that object that is begin pointed to. 

The trick here is that if we are managing memory directly, then we might struggle when we throw an exception. 
```
void f(){ 
	int *ip = new int(42); // dynamically allocate a new object
	// we are in charge of this
	// code that throws an exceptions that is not caught inside f
	delete ip;
}
```
This memory cannot be freed then. 
There is no pointer to this memory outside the function `f`. There is therefore no way to free this memory. 

#### Smart Pointers, Dumb Classes
All the library classes, will define destructors that take care of the cleaning up of resources used by that object. 
Not all classes are so well behaved. 

Classes that allocate resources - and do not define a destructor to free those - can be subject to the same kind of errors that arise when we use dynamic memory. 
Similarly we don't want some memory to leak. 

We might have something like this: 
```
struct destination; // represents what we are connecting to
struct connection; // information needed for the connection
connection conect(destination*); // open the connection
void disconnect(connection); // close the given connection
void f(destination &d /*other parameters */)
{ 
	// get a connection; must remember to close it when doen
	connection c = connect(&d);
	// use the connection
	// if we forget to call disconnect before exiting f; there will be no way to close c
}
```

If `connection` had a destructor, that destructor would automatically close the connection when `f` completes. 
It doesn't have on right now. 
This problem is nearly identical to our previous program that used a `shared_ptr`. 

Using `shared_ptr` to close something as well is also fine, in the sense of freeing heap memory that we have allocated for. 

#### Using our own Deletion Code
By default `shared_ptr` assumes that it is pointed to dynamic/heap memory. 
When `shared_ptr` is destroyed, it actually uses `delete` in order to get rid of the memory it is pointing to. 
If we don't want to use `delete` in its simplest form, then we might want to define delete. It must be possible to call this `deleter` function with the pointer stored inside the `shared_ptr`. 

```
void end_connection(connection *p){ disconnect(*p);}
// end_connection takes p, a pointer to a connection and disconnect takes in the actual connection
```
Here we are using p in order to pass to disconnect the connection itself. 

Therefore when we create a `shared_ptr` we have to make sure that we pass in the extra delete function. 

```
void f(destinationf &d /* other parameters */)
{ 
	connection c = connect(&d);
	shared_ptr<connection> p(&c, end_connection);
	// use the connection
	// when f exits, even if by an exception, the connection will be properly closed
}
```

When p is destroyed, it won't execute a `delete`w on its stored pointer. 
`p` will call `end_connection` on that pointer. 
In turn, in this example, this will call `disconnect`. 
Here, it's more apt to point out that when the exception is thrown, `p` is out of scope and destroyed. 

##### Smart Pointer Pit Falls
- Don't use the same built-in pointer value to initialize (or `reset`) more than one smart pointer.
- Don't `delete` the pointer returned from `get()`.
- Don't use `get()` to initialize or `reset` another smart pointer. 
- If you use a pointer returned by `get()`, remember that the pointer will become invalid when the last corresponding smart pointer goes away.
- If you use a mart pointer to manage resource other than memory allocated by `new`, remember to pass a deleter. 
- 