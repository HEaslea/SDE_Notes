Member functions other than constructors and assignment can benefit from providing both copy and move versions. 
Such move-enabled members typically use the same parameter pattern as the copy/move constructor and the assignment operators - one version takes an lvalue reference to `const` , and the second takes and rvalue reference to `nonconst`. 
Remember that we can pass an rvalue into a function that has this paramtype `(const T& param)`. However, this passing of arguments will prefer to use `(T&& param)`. 

Library containers that define `push_back` provide two versions: 
One that has an rvalue reference parameter and the other a `const` lvalue reference. 
Assuming that `X` is the element type, these containers define: 
```
void push_back(const X&); // copy binds to any kind of X
void push_back(X&&); // move; binds only to modifiable rvalues of type X
```

The first version will copy data from our type `X`. 
We can only pass an rvalue that is not `const` to the second version. 
This is an exact match for non`const` rvalues and will be run when we pass a modifiable rvalue. 
This version is free to steal resources from its parameter. 
Remember that the main idea here is that if we pass in an object, we might not want to take away the values from the object. However, if we pass on an rvalue, we know that it will be temporary and destroyed soon-ish, therefore we can take what we want from it. 

Normally, there is no need to define versions of the operation that take a `const X&&` or plain `X&`. 
Usually, we want to pass an rvalue when we want to move from or "steal" from. 
This is because, that rvalue will not be available after that function call. 
Therefore, here the argument must not be `const`. 
Copying from an object should not change the object being copied. 
There is therefore usually no reason to define a version that take a (plain) `X&` parameter. 

So copying : `const T&`
moving : `T&&`

So let's give our make-shift `StrVec` a second version of `push_back`. 
```
class StrVec{ 
public: 
	void push_back(const std::string&); // copy the elemnt
	// it remains const, we aren't changing it

	void push_back(std::string&&); // moving the element

	// other members as before
};

// unchanged from the original version
void StrVec::push_back(const string& s)
{ 
	chk_n_alloc(); // ensure that there is room for another element
	// construct a copy of s in th element to which first_free points
	alloc.construct(first_free++, s);
}

void StrVec::push_back(string &&s)
{
	chk_n_alloc(); // reallocates the StrVec if necessary
	alloc.construct(first_free++, std::move(s));
}
```

They are nearly identical. 
As `construct` function uses the type of its second and subsequent arguments to determine which constructor to use. 
`move` returns an rvalue ref, the type of the argument to `construct` is `string&&`. 
Therefore the `string` move constructor will be used to construct a new last element. 

```
StrVec vec; // emptry StrVec
string s = "Some string or another";
vec.push_back(s); // calls push_back(const string&)
vec.push_back("Done"); // calls push_back(string&&)
```


##### Rvalue and Lvalue Reference Member Functions
When we call a member function on an object, regardless of whether that object is an lvalue or an rvalue. 
```
string s1 = "a value", s2 = "another";
auto n = (s1 + s2).find('a');
```
We are calling `find` member on the `string` rvalue that results from adding two `string`s. 

```
s1 + s2 = "wow!";
```
Here we assign to the rvalue result of concatenating these `string`s. 

We are allowed to assign to rvalues, but we might want to prevent this type of usage in our own classes. 

Here, we want to force the left hand operand to be an lvalue. 

We indicate the lvalue/rvalue property of `this` in the same way that we define `const` member functions. 
#referencequalifier
We place a **reference qualifier** after the parameter list: 
```
class Foo{ 
public: 
	Foo& operator=(const Foo&)&; // may assign only to modifiable lvalues
	// other members of foo
};

Foo& Foo::operator=(const Foo& rhs) &
{ 
	// do whatever is needed to assign rhs to this object
	return *this;
}
```
The reference qualifier can be either & or &&, indicating that `this` may point to an rvalue or lvalue, respectively. 
Like the `const` qualifier, a reference qualifier may appear only on a (non`static`) member function and must appear in both the declaration and definition of the function. 



