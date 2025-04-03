#unique_ptr
A `unique_ptr` owns the object to which it points. 
Only one can be real at a time, and only one to point to a object at any one point. 
The object is destroyed when the `unique_ptr` is destroyed, goes out of scope. 
![[Pasted image 20240409010103.png]]There is no `make_shared` for `unique_ptr`, therefore we just defined, we bind it to a pointer used by `new`. This is using the direction form of the initialization. 
```
unique_ptr<double> p1; // creating a unique_ptr that can point to a double
// this doesn't yet point to an actual number

unique_ptr<int> pi(new int(42)); // unique_ptr to int of 42
```
Because it owns the object to which it points, `unique_ptr` does not support copy or assignment. 
```
unique_ptr<string> p1(new string("Stegosaurus"));

unique_ptr<string> p2(p1); // error no copy for unique_ptr

unique_ptr<string> p3;
p3 = p2; // another error, no assign for unique_ptr
```

Why would you be able to assign or share, it's unique, only one object that can be pointed to in this way. 

However we may want to transfer ownership from one (non`const`) to another by calling `release, reset`: 
```
// transfers ownership from p1 (which points to the string stegosaurus) to p2
unique_ptr<string> p2(p1.release()); // release makes p1 null
// doesn't delete the object

unique_ptr<string> p3(new string("Trex"));
// transfer ownership from p3 to p2
p2.reset(p3.release()); // p2 reset using p3 release pointer
```

`release` will return the pointer currently stored in the `unique_ptr` and makes that pointer null. Thus `p2` is initialized from the pointer value that had been stored in `p1` making `p1` null. 

`reset` takes an optional pointer and repositions the `unique_ptr` to point to the given pointer. 
If the `unique_ptr` is not null, then the object to which the `unique_ptr` had pointed is deleted. 
The call to `reset` on `p2` therefore frees the memory used by the `string` initialized from `"Stegosaurus"`, transfers `p3`s pointer to `p2` and makes `p3` null. 
`reset` just repoints the pointer, makes it point to the given pointer given in the arguments. 
Remember that if we `release` an object from a smart pointer to a regular pointer, then we are in charge of when the object is deleted. 

```
// we couldnt' do something like this
p2.release(); // WRONG p2 won't free the memory and we've lost the pointer

auto p = p2.release(); // ok, but we must remember to delete(p) at some point
```

#### Passing and Returning `unique_ptr`s
#passinguniqueptrs #returninguniqueptrs
There is one exception to the rule that we cannot copy a `unique_ptr`. 
We can copy or assign a `unique_ptr` that is about to be destroyed.
The most common example is when we return a `unique_ptr` from a function

```
unique_ptr<int> clone(int p)
{ 
	// ok explicitly create a new unique_ptr<int> from int*
	return unique_ptr<int>(new int(p));
}
```
We can also return a copy of a local object: 
```
unique_ptr<int> clone(int p)
{ 
	unique_ptr<int> ret(new int(p));
	// ... 
	return ret;
}
```
The compiler is aware that the object it is returning is about to be destroyed then will pull a "special copy" which we'll discuss later. 

It's called `Move Constructor and Move Assignment`

#### Passing a `deleter` to `unique_ptr`
The way that `unique_ptr`s work with deleters is slightly differently than that of the `shared_ptr`. 
We have to supply the deleter type inside the angle brackets along with the type to which the `unique_ptr` will point to. 
```
// p points to an object of type objT and uses an object of type delT to free that object
// it will casll an object named fcn of type delT

unique_ptr<objT, delT> p(new objT, fcn);
```
```
void f(destination &d /* other needed parameters */)
{ 
	connection c = connect(&d); // open a connection
	// when p is destroyed, the connection will be closed
	unique_ptr<conneciton, decltype(end_connection)*> 
		p(&c, end_connection); 
	// this is done the same way that we created an ordering predicate in associated containers

	// use the connection, do what you want. 

	// when f exits, even if by an exception, the connection will be properly closed
}
```

Here we used `decltype` to specify the function pointer type. 
Remember that we are using a pointer to a function here. 



