#weakptr

Another smart pointer, does not control the lifetime of the object. 
They point to an object that is managed by a `shared_ptr`. 
It will not change the reference count either. 

Still, when the last `shared_ptr` is lost, the object will be destroyed, even if a `weak_ptr` is pointing to it, hence the "weak" aspect of it. 

![[Pasted image 20240409161049.png]]
We have to initialize from a `shared_ptr`.

```
auto p = make_shared<int>(42);
weak_ptr<int> wp(p); // wp shares with p weakly ; use count of p is unchanged
```

It is possible here that the object to which `wp` points to is destroyed. 
As this might happen,  we cannot use the `weak_ptr` to access the object directly. 
In order to get there, we have to call `lock`. 
This will check whether the object still exists. 
If so, `lock` will return a `shared_ptr` to that object. 
```
if(shared_ptr<int> np = wp.lock()){ // true if np is not null
	// inside the if, np shares its object with p 
}
```

#### Checked Pointer Class 
As an illustration of how this is useful, we'll define a companion pointer class for our `StrBlob` class. 
`StrBlobPtr` will store a `weak_ptr` to the `data` member of the `StrBlob` from which it was initialized. 
By using a `weak_ptr` we don't affect the lifetime of the `vector` to which a given `StrBlob` points. 
However, we can prevent the user from attempting to access a `vector` that no longer exists. 

`StrBlobPtr` will have two data members: `wptr` which is either null or points to a `vector` in a `StrBlob` and `curr` which is the index of the element that this object currently denotes: 
Like its companion `StrBlob` class, our pointer class has a `check` member to verify that it is safe to dereference the `StrBlobPtr`: 
```
class StrBlobPtr{ 
public: 
	StrBlobPtr(): curr(0) {}
	StrBlobPtr(StrBlob &a, size_t sz = 0): wptr(a.data), curr(sz) {}
	std::string& deref() const;
	StrBlobPtr& incr(); // prefix version
	
private:
	// check returns a shared_ptr to the vector if the check succeeds
	std::shared_ptr<std::vector<std::string>> check(std::size_t, const std::string&) const;
	// store a weak_ptr, which means the underlying vector might be destroyed
	std::weak_ptr<std::vector<std::string>> wptr;
	std::size_t curr; // current position within the array
}
```

It's worth nothing that we cannot bind a `StrBlobPtr` to a `const StrBlob` object. 
This is because the constructor here takes a reference to a non`const` object of type `StrBlob`
The `check` member differs from the one in `StrBlob` because it must check whether the `vector` to which it points is still around. 

```
std::shared_ptr<vector<string>> 
	StrBlobPtr::check(std::size_t i, const std::string& msg)const
{ 
	auto ret = wptr.lock(); // is the vector still around?
	if(!ret)
		throw std::runtime_error("unbound StrBlobPtr");
	if(i >= ret->size())
		throw std::out_of_range(msg);
	return ret; // otherwise, we're all good, return a shared_ptr to the vector
}
```


#### Pointer Operations
We'll learn to define our own operations later one. 
We've defined definitions named `deref` and `incr` to dereference and increment the `StrBlobPtr` (perfectly valid idea if we want to avoid operator overloading). 

The `deref` member calls `check` to verify that it is safe to use the `vector` and that `curr` is in range: 
```
std::string& StrBlobPtr::deref() const
{ 
	auto p = check(curr, "dereference past end");
	return (*p)[curr]; // dereference p here to allow for subscripting
}
```
The idea of the string literal here is that we pass the `e.what()` into the member that has the throw exception up.

The `incr` member also calls `check`: 
```
// prefix return a reference to the incremented object implementation here
StrBlobPtr& StrBlobPtr::incr()
{ 
	// if curr already points past the end of the container, then can't increment any further
	check(curr, "increment past end of StrBlobPtr");
	++curr; // advance the current state
	return *this;
}
```
Remember that `this` is a special kind of pointer. 

We'll also give our class a `begin` and `end` operation.  

These will return `StrBlobPtr`s pointing to the first or one past the last element in `StrBlob` itself. 

We must also make `StrBlobPtr` a `friend` of `StrBlob`. 

```
class StrBlob{ 
	friend class StrBlobPtr;
	// other members as in the earlier pages
	StrBlobPtr.begin(); // return StrBlobPtr to the first element
	StrBlobPtr.end(); // and one past the last element
}
```
Friend allows us access to the private and protected members of the first class, without, having to inherit anything. 
These will not be able to be defined until everything else has been defined, that is why we have declarations in general. 
```
StrBlobPtr StrBlob::begin() { return StrBlobPtr(*this);}
StrBlobPtr StrBlob::end() { return StrBlobPtr(*this, data->size());}
```


