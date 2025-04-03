 Normally these are nonmember functions in order to allow conversions for either the left or right hand operand. 
 Operands are often referred to as `const`. 

There will be a value as a result. 
That is usually distinct and held in the local variable. 
Then we return a copy of this local as its result. 

We should usually define the corresponding compound assignment operator as well. 

```
// assumes that both objects refer to the same book
Sales_data
operator+ (const Sales_data &lhs, const Sales_data &rhs)
{ 
	Sales_data sum = lhs; // copy data members from lhs into sum
	sum += rhs; /// add rhs into sum
	return sum;
}
```

This is so close to our add function that we had earlier. 
Then we return a copy of `sum`. 

Classes that define both arithmetic operator and the related compound assignment ordinarily ought to implement the arithmetic operator by using the compound assignment. 
The compound assignment here is `+=`. 

#### Equality Operators
#equalityoperatoroverload
Returning `bool`s. 
Usually, this looks at all the data members, to see if they are all the same, before returning a bool or anything: 
```
bool operator==(const Sales_data &lhs, 
	const Sales_data &rhs)
{ 
	return lhs.isbn() == rhs.isbn() && lhs.units_sold == rhs.units_sold && lhs.revenue == rhs.revenu;
}
bool operator!=(const Sales_data &lhs, const Sales_data &rhs)
{ 
	return !(lhs == rhs); // using == operator here
}
```

- Users expect to be able to `object == object`, so that we don't have to define all other manner of functions in order to compare two objects. 
- `==` should compare given objects and the equivalent data. 
- This should be transitive `a==b`, and `b==c`, therefore `a==c`. 
- If we define the `operator==` we should also define `operator!=`, this will be expected by users first and foremost. 
- One of these should delegate the work to the other. 



##### Relational Operations
#relationaloperationoverload
These are just useful, nifty things to have. 
Ordinarily the relational operators should: 
1. Define an ordering relation that is consistent requirements for use as a key to an associative container!
2. Define a relation that is consistent with `==` if the class has both operators. eg. Two objects are `!=` then once object should be `<` the other. 

It depends which variable that we want to compare, eg. how is a vector `<` than another eg. is it there magnitude. 
If there is no single definition of `<` then we have to assume that the class will not benefit from `<`. 
`==` and `<` have to have consistent results. 

##### Assignment Operators
In addition to copy and move operators that assign one object of class type to another of the same type. 
We can create assignment operators in order to allow other types of RHO. 

Just like vector, that assigns another assignment that takes a braced list (remember in the Effective Modern C++). 
eg.
```
vector<string> v;
v = {"a", "an", "the"};
```
We could add that to our own version of `StrVec`. 
```
class StrVec{ 
public: 
	StrVec& operator=(std::initializer_list<std::string>);
	// other members
};
```

We have to return a reference to LHO. 

```
StrVec& StrVec::operator=(initializer_list<std::string> il)
{ 
	// alloc_n_copy allocates space and copies elements from the given range
	auto data = alloc_n_copy(il.begin(), il.end());
	free(); // destroy the ele in this object and free space
	elements = data.first; // update data mems to point to the new space
	first_free = cap = data.second;
	return *this;
}
```
##### Compound Assignment Operators
Are not required to be members. 
They are the `+=` etc. 
Like with most assignment operators, we have to return a reference to their LHO. 

```
// member binary operator: LHO == this pointer
// assumes that both objects refer to the same book
Sales_data& Sales_data::operator+=(const Sales_data &rhs)
{ 
	units_sold += rhs.units_sold;
	revenue += rhs.revenue; 
	return *this;
}
```
> Assignment operators must, and ordinarily compound-assignment operators should, be defined as members. These operators should return a reference to the LHO

##### Subscript Operator
Classes that represent containers from which elements can be retrieved by position often define subscript operator, `operator[]`. 
> These have to be a member function

Returning a ref, means that our subscript can be used on either side of an assignment. 
Usually a good idea to define both `const` and non`const` version of this operator. 
When applied to `const` objects, subscript should return a ref to `const`l so that it is not possible to assign to the returned object. 

So here we need two, one that is `const`and another that is `nonconst`. 

Defining subscript for `StrVec`. 
```
class StrVec{ 
public: 
	std::string& operator[](std::size_t n)
		{return elements[n];}
	const std::string& operator[](std::size_t n) const
		{return elements[n];}
	// other members as per
private: 
	std::string* elements; // pointer to the first element
};
```

using the `nonconst`, we can assign to that element too, if we subscript a `const` object, we can't: 
```
// assume that svec is a StrVec
const StrVec cvec = svec; // copy elements from svec into cvec
if(svec.size() && svec[0].empty())
{ 
	svec[0] = "zero";// this is fine
	cvec[0] = "Zip"; // error subscripting cvec returns a reference to const
}
```

##### Increment and Decrement Operator
These are most found in the iterator classes. 
Most often they change the state of the object, therefore we should make them members of a class, where `this` is bound to LHO. 

We should define both the prefix and postfix. 
Returning a reference to the object. 
We also need to check if the index is valid, for postfix and prefix.

```
// prefix: return a ref to the incremented/decremented object
StrBlobPtr& StrBlobPtr::operator++()
{ 
	// if curr already points past the en, can't increment
	check(curr, "increment past end of StrBlobPtr");
	++curr; // advance the current state
	return *this;
}

StrBlobPtr& StrBlobPtr::operator==()
{ 
	--curr; // move the current state back on element
	check(curr, "Decrement past being of StrBlobPtr");
	return *this;
}
```

##### Differentiating Prefix and Postfix Operators
The pre and post use the same symbol, meaning that the overload versions of these still use the same name. 
To solve this, the postfix version take an extra (unused) parameter of type `int`. 
Try not to use this extra parameter. 

So there are postfix operators to `StrBlobPtr`: 
```
class StrBlobPtr
{
public: 
	// increment and decrement
	StrBlobPtr operator++(int); // postfix operator
	StrBlobPtr operator--(int); // postfix as well
	// other members as before
};
```

> To be consistent, the postfix should return the old (un-incremented or un-decremented) value. That value is returned as a value, not a reference. 

Implementation: 
```
// postfix; incremenet/decrement the object but return the unchanged value
StrBlobPtr StrBlobPtr::operator++(int)
{ 
	// no check needed here; the call to prefix will do that
	StrBlobPtr ret = *this; // save the current value
	++*this; // advance on element; prefix++ 
	// prefix will check the increment
	return ret;
}

StrBlobPtr StrBlobPtr::operator--(int)
{ 
	StrBlobPtr ret = *this; // save current value
	--*this; // decrement
	return ret
}
```

Usually using the prefix in the postfix. 

##### Calling the Postfix Operators Explicitly
If we do, then we do have to pass an `int`. 
```
StrBlobPtr p(a1); // p points to the vector inside a1
p.operator++(0); // call postfix operator++
p.operator++(); // call prefix operator
```
Just remember that the postfix intakes an `int` over the prefix which doesn't. 

##### Member Access Operators
The dereference `(*)` and arrow `(->)` operators are often used in classes that represent iterators and in smart pointer classes. 

```
class StrBlobPtr{ 
public: 
	std::string& operator*() const
	{ 
		auto p = check(curr, "dereference past end");
		return (*p)[curr]; // (*p) is the vector to which this
		// object points
	}
	
	std::string* operator->() const
	{ 
		// delegate the real work to the dereference operator
		return & this->operator*();
	}
	// all other members as before
}
```
The arrow avoids any work, by just using dereference. 

Operator arrow must be a member. The dereference operator is not require to be one, but usually should be too. 

Using `const` as we don't change the objects that are passed in. 
Can't do this with the decrement and the increment. 

We can use these operators the same way that we've used the corresponding operations on pointers or `vector` iterators: 
```
StrBlob a1 = {"Hi", "bye", "now"};
StrBlobPtr p(a1); // p points to the vector inside a1
*p = "okay"; // assigsn to the first element in a1
cout << p->size() << endl; // pritns 4, the size fo the first element in a1
cout << (*p).size() << endl; // equiv to p->size()
```

##### Constraints on Return from Operator Arrow
It's not a great idea, but we can define `operator*` to do whatever processing that we like. 
The same is not true of the arrow. The arrow operator never loses its fundamental meaning of member access. 
We cannot change the fact that the arrow fetches a member. 

When we write `point->mem`, `point` must be a pointer to a class object or it must be an object of a class with and overloaded `operator->`. 
It has to be equivalent to
```
(*point).mem; // pionts is a built-in pointer type
pointer.operator()->mem; // point is an object of class type
```
Otherwise we run into an error. That is, `point->mem` executes as follows: 
1. if `point` is a pointer, then the built-in arrow operator is applied, meaning the expression is a synonym of `(*point).mem`. The pointer, dereference, and the indicated member is fetched from the resulting object. If the type pointed to by `point` does not have a member named `mem`, then the code is in error. 
2. If `point` is an object of a class that defines `operator->`, then the result of `point.operator->()` is used to fetch `mem`. If that result is a pointer, then step1 is executed on that pointer. If the result is an object that itself has an overloaded `operator->()`, then this step is repeated on that object. The process continues until either a pointer to an object with the indicated member is returned or some other value is returned, in which case the code is in error. 


> The overload arrow operator **must** return either a pointer to a class type or an object of a class type that defines its own operator arrow. 

I think what it's trying to say is that when we have an object, rather than a pointer, and we use `object.operator()->mem`. 


So this is a pretty good idea, that at some point the arrow is going to need a pointer to an object. 

```
class MyObject{ 
public: 
	void doSomething() { 
		std::cout << "Doing something" << std::endl;
	}
};

class ObjectProxy{ 
private: 
	MyObject obj;

public: 
	MyObject* operator->() { 
		return &obj;
	}
};

int main(){ 
	ObjectProxy proxy; 
	proxy->doSomething(); // Accessing member function of
	// MyObject via operator->
}
```
So here we are calling the `operator->` on an object, it calls that function. We get a pointer there, and then the -> will do it's proper usage, that is built-in, and we can't override that aspect of it. 

Eventually we get to a pointer, that will return a member.
If we dereference using `*` then we can return whatever we like, however, the arrow has to access something. 

>  The overload arrow operator **must** return either a pointer to a class type or an object of a class type that defines its own operator arrow. 

