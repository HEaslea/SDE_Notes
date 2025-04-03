Simply a blueprint for generating classes: 
To use, we supply additional information inside the angle brackets. 
The idea here is that we don't have to do this for template functions: 
ie. with the `compare` function: 
```
compare(10,20); // will be instantiated with int
compare<int>(10,20); will do the same thing
```

For classes we have to do the second thing. 
#### Defining a Class Template
Essentially the same.: 
```
template <typename T> 
class Blob{ 
// class bob utilising T 
	typedef T value_type;
	typedef typename std::vector<T>::size_type size_type;
	
	// constructors
	Blob();
	Blob(std::initializer_list<T> il);

	// number of elements in the Blob
	size_type size() cosnt { return data->size(); }
	bool empty() const { return data->empty(); }

	// add and remove elements
	void push_back(const T &t) { data->push_back(std::move(t));}
	void pop_back();

	// element access
	T& back();
	T& operator[](size_type i);

private: 
	std::shared_ptr<std::vector<T>> data;

	// throw msg if data[i] isn't valid
	void check(size_type i, const std::string &msg) const;
};
```
`T` refers to the type of object that the object will hold. 

#### Instantiating a Class Template
**Explicit Template Arguments** is the list that we provide to a "thing" that is instantiated with a template. 
Defining a blob object here will look like this: 
```
Blob<int> ia; // Empty Blob<int>
Blob<int> ia2 = {0,1,2,3,4}; // Blob<int> with five elements
```

The compiler will instantiate the whole class with an `int` type, using a type specialization: 
```
template<> class Blob<int>{
	// etc.
};
```

We can just write these on our own as well.

Whenever we use a different type, then the compiler will instantiate another class: We might want to specify how a class of a certain type. 

> Each instantiation of a class template constitutes an independent class. 

### References to a Template Type in the Scope of the Template
Just the idea of using `T` for the type that we originally passed in: 
```
std::shared_ptr<std::vector<T>> data;
```
Where we pass in the type that we took into the class itself. 
Then when we instantiate using `string`, it will look something like this: 
`shared_ptr<vector<string>>`


#### Member Functions of Class Templates
Members defines inside the class body are implicitly inline. 
If we define a `StrBlob` member like this: 
```
ret-type StrBlob::member-name(param-list)
```
The corresponding `Blob` member will look like
```
template<typename T> 
ret-type Blob<T>::member-name(parm-list)
```

The idea is that if we have a class template, the member function will essentially be normal functions, however, the compiler will need to instantiate each function, depending on which Type that we enter in. 

A member function defined outside the class template body **starts with the keyword `template` followed by the class' template parameter list**. 

A clearer example here is: 
```
template <typename T> 
class MyTemplateClass{ 
public: 
	void display(); // looks pretty normal here
};

template <typename T> 
void MyTemplateClass<T>::display() { 
	cout << "Data: " << data << endl;
}

int main(){ 
	MyTemplateClass<int> obj1(10);
	obj1.display(); // Instantiating Int versions of class and member function

	MyTemplateClass<string> obj2("Hugo");
	obj2.display(); // instantiating based on strings, class, and therefore member functions
}
```
### The `check` and Element Access Members
#elementaccess
We'll start by defining the `check` member for the class we are talking about, using what we have just spoken about: 
```
template <typename T> 
void Blob<T>::check(size_type i, const std::string &msg) const
{ 
	if(i >= data->size())
		throw std::out_of_range(msg);
}
```

Note as well that we have to use `Blob<T>`. 
This is identical to the original `StrBlob` member. 
The subscript operator and `back` function use the template parameter to specify the return type but are otherwise unchanged. 
```
template<typename T> 
T& Blob<T>::back()
{ 
	beck(0, "back on emptry Blob");
	return data->back();
}

template<typename T> 
T& Blob<T>::operator[](size_type i)
{ 
	// if i > sizeof(), check will throw, preventing access, 
	changing flow control 
	check(i, "subscript out or range");
	return(*data)[i];
}
```

#### Blob Constructors
Writing the template class constructor like this: 
```
template <typename T>
Blob<T>::Blob() : data(std::make_shared<vector<T>>) {}
```

The idea here is that we are defining the member named `Blob` in the scope of `Blob<T>`. 
```
template <typename T> 
Blob<T>::Blob(std::initializer_list<T> il)
	: data(std::make_shared<std::vector<T>>(il)) {}
```
This is used to initialize the object from an `initializer_list`. 

`T` being in both here, means that, at the very least, we are passing in the same type to both. 

`Blob<string> articles = {"a", "a", "the"};`

The letter that we use does not need to be the same. 
```
template <typename U> 
Blob<U>::Blob() : /.../
```
Here we can use U, however, it doesn't show consistency or is helpful at all.

#### Instantiation of Class-Template Member Functions
The member function, by default, will only be instantiated iff the program uses that member function: 
```
// instantiates Blob<int> and intializer_list<int> constructor
Blob<int> squares = {0,1,2,3,4,5,6,7,8,9};
// instantiates Blob<int>::size() const
for(size_t i = 0; i != squares.size(); ++i)
	squares[i] = i*i; // instantiates Blob<int>::operator[](size_t)
```

> By default, a member of an instantiated class template is instantiated only if the member is used

#### Simplifying Use of a Template Class Name inside Class Code
There is one exception to the rule that we must supply template arguments when we use a class template type. 
Inside the scope of the class template itself, as demonstrated earlier, we don't need the argument: 
```
// BlobPtr throws an exception on attempts to access nonexistent elements
template <typename T> class BlobPtr
{ 
public: 
	BlobPtr() : curr(0) {}
	BlobPtr(Blob<T> &a, size_t sz = 0) 
		: wptr(a.data), curr(sz) {}

	T& operator*() const
	{ 
		auto p = check(curr, "dereference past end");
		return (*p)[curr];
	}

	// increment and decrement
	BlobPtr& operator++(); // prefix operators
	BlobPtr& operator--();
	// notice here that we didn't use Blob<T>

private: 
	// check returns a shared_ptr to the vector if the check succeeds
	std::shared_ptr<std::vector<T>>
		check(std::size_t, const std::string&) const;
	std::weak_ptr<std::vector<T>> wptr;	
	std::size_t curr; // current position within the array
};
```



Remember that to instantiate a postfix operator of either + or -. 
We would write the same as the prefix, however, taking in an int as well, that's how the compiler will differentiate between the two: 
```
MyObject operator++ (int){ /* ... */ }; // postfix object++
MyObject operator++ () { /* ... */ }; // prefix ++object
```

#### Using a Class Template Name outside the Class Template Body
When we define members outside the body of a class template, remember that we are no longer in the scope of the class until the class name is seen. 

```
// postfix increment / decrement the object but return the unchanged value
template <typename T>
BlobPtr<T> BlobPtr<T>::operator++(int) // param int makes postfix
{ 
	// no check needed here, the call to prefix increement will do the check
	BlobPtr ret = *this; // save the current value
	++*this; // advance on element; prefix++ checks the validity

	return ret;
}
```
When we define the `BlobPtr<T>` return type, the compiler will know that we are returning the same type as the class template itself. 
The definition of `ret` here is the same as saying `BlobPtr<T> ret = *this;`. 

> Inside the scope of a class template, we may refer to the template without specifying template argument(s).

#### Class Templates and Friends
A class template that has a nontemplate friend grants that friend access to all the instantiations of the template. When the friend itself is a template, the class granting friendship controls whether friendship includes all instantiations of the template or only specific instantiation(s). 

###### One-to-One Friendship
#templatefriendship
The most common form of friendship from a class template to another template (class or function), establishes friendship between corresponding instantiations of the class and its friend. 
Eg. our `Blob` class should declare the `BlobPtr` class and a template version of the `Blob` equality operator as friends. 
In order to refer to a specific instantiation of a template (class or function) we must first declare the template (class or function) itself. 
A template declaration includes the template's template parameter list: 
```
// forward declarations needed for friend declarations in Blob

template <typename> class BlobPtr; // no T required
template <typename> class Blob; 
template <typename T> 
	bool operator==(const Blob<T>&, const Blob<T>&);
	
template <typename T> class Blob{ 
	// each instantiation of Blob grants access to the version of
	// BlobPtr and the equality operator instantiated with the same type
	friend class BlobPtr<T>;
	friend bool operator==<T>
		(const Blob<T>&, const Blob<T>&);
	// other members as before
};
```

Start by declaring that `Blob, BlobPtr, operator==` are templates. 
These are required for the parameter declaration in the `operator==` function and the friend declarations in `Blob`.

The friend declarations use `Blob`'s own template argument. 
Thus the friendship is restricted to those instantiation of `BlobPtr` and the equality operator that are instantiated with the same type. 
```
Blob<char> ca; // BlobPtr<char> and operator==<char> are friends

Blob<int> ia; // BlobPtr<int> and operator==<int> are friends
```
Essentially, the friends have to use the same instantiations that Blob does. 
Here;
`Blob<char>` Has access to non`public` members of `ca`, but `ca` has no special access to `ia` or any other `Blob<int>`, or any other instantiation of `Blob`. 

### General and Specific Template Friendship
A class can also make every instantiation of another template it's friend, or it may limit friendship to a specific instantiation: 
```
// forward declaration necessary here, in order to befriend a specific instantiation of a template
template <typename T> class Pal;
class C{ // C is an ordinary, nontemplate class
	friend class Pal<C>; // Pal instantiated with class C is a friend to C
	
	// all instances of Pal2 are friends to C;
	// now forward declaration required when we befriend all instantiations
	template <typename T> friend class Pal2;
	// befriending all instantiations
};

template <typename T> class C2{ 
	// C2 is itself a class template
	// each instantiation of C2 has the same instance of Pal as a friend
	friend class Pal<t>; // a template declaration for Pal must be in scope

	// all instances of Pal2 are friends of each instance of C2; no prior declaration needed
	template <typename X> friend class Pal2;

	// Pal3 is a nontemplate class that is a friend of every instance of C2
	friend class Pal3; // prior declaration not needed
};
```
Just think here what we are instantiating various types of the class. 

To allow all instantiations as friends, the friend declaration must use template parameter(s) must differ form those used by the class itself. 

###### Befriending the Template's Own Type Parameter
The type of the template type parameter will be a friend;
```
template <typename Type> class Bar{ 
	friend Type;
	//... 
}
```

Whatever type is used to instantiate `Bar` is a friend as well. 

If we instantiated `Bar<Foo>` then `Foo` would be a friend of `Bar<Foo>`. 

#### Template Type Aliases
An instantiation of a class template defines a class type, as with any other class type, we can define a `typedef`, referring to that instantiated class: 
`typedef Blob<string> StrBlob;`

Allowing us to run code written earlier, using our template version of `Blob` instantiated with `string`. 
A template is not a type, we can't define a `typedef` that refers to a template. 
There is no way to define a `typedef` that refers to `Blob<T>`. It just wouldn't make sense. 

HOWEVER, there is a new standard, letting us define a type alias for a class template.
```
template <typename T> using twin = pair<T,T>;
twin<string> authors; // authors is a pair <string,string>
```

This is kind of glossed over, but I think it's sort of useful: 
```
template <template-parameter-list>
using identifier attr(optional) = type-id;
```
```
template<class T>
struct Alloc{};

template<class T>
using Vec = vector<T, Alloc<T>> 

Vec<int> v; // Sames as vector<int, Alloc<int>>
```

```
template <typename...> 
using void_t = void;

template <typename T> 
void_t<typename T::foo> f();
```


```
using flag = std::ios_base::fmtflags;
flags fl = std::ios_base::dec;

using func = void (*) (int, int);

void example(int, int) {}
func f = example; // fine 


template <class T> 
using ptr = T*;
now `ptr<T>` is an alias for pointer to T
ptr<int> x; // pointer to an int


// type alias for hiding a template parameter
template <class CharT>
using mystring = std::basic_string<CharT, std::char_traits<CharT>>; 

mystring<char> str; // somewhat hiding the template parameter of std::basic_string

// introducing a member typedef name
template<typename T> 
struct Container { using value_type = T; };

// which can be used in generic programming
// generic programming is writing code for types that will be instantiated later (run-time)
template<typename ContainerT>
void info(const ContainerT& c)
{ 
	typename ContainerT::value_type T;
	//
}
```

```
template<typename T> using twin = pair<T,T>;
twin<string> authors; // pair<string, string>
```
`twin` is a synonym for `pair`s in which the members are same type. 

```
template<typename T, typename U> using twin = pair<T,U>;
twin<char, string> thing; // pair<char, string> 
```
But here you can just use `pair<char, string>`. 

`twin<int> win_loss; // win_loss is a pair<int, int>`. 
`twin<double> area; // area is pair<double, double>`

We can fix one or more of the template parameters: 
```
using iPair = pair<int, int>;
template<typename T> using iP = pair<T, int>;

// then the second is initialized with
iP<string> stringInt = {"hugo", 10};
// this is fine
```


```
template <typename T> using partNo = pair<T, unsigned>;
partNo<string> books; // pair<string, unsigned>
partNo<Vehicle> cars; // pair<Vehicle, unsigned>
partNo<Student> kids; // pair<Student, unsigned>
```


We just define the one `partNo<onlytypeparameter>`, the second is already defined. 

##### `static` Members of Class Templates
#templateclassstaticmembers
```
template <typename T> class Foo{ 
public: 
	static std::size_t count() { return ctr; }
	// other interface members
private: 
	static std::size_t ctr;
	// other implementation members
};
```

Each instantiation of `Foo` has its own instance of the `static` members. 

For a given type `X`, there is one `Foo<X>::ctr` and one `Foo<X>::count` member. 
All objects, therefore, of type `Foo<X>` will share the same `ctr` object and `count` function. 
`Foo<int> f1, f2, f3; //will all share Foo<int>::ctr`
`Foo<int>::count() too`. 

Defining this static member slightly differently:
```
template <typename T> 
size_t Foo<T>::ctr = 0; // define and initialize ctr;
// remember this needs to be done outside the class
```

This will be only for type `T`: 
```
Foo<int> fi; // instantiates Foo<int> class
// and the static data member ctr

auto ct = Foo<int>::count(); // instantiates Foo<int>::count
ct = fi.count(); // using Foo<int>::count
ct = Foo::count(); // ERROR which instantiation
```

### Template Parameters
#templateparameters
[[Template Parameters]]










My Notes on Template Friendship that I'm reading about: 
```
template <class T> class BE{ 
	template<class T> friend class BT:
};
```
This is not allowed, template parameters cannot shadow each other. Nested templates must have different template parameter names. 
So do this instead: 
```
template <typename T> 
struct foo{ 
	template<typename U>
		friend class bar;
};
```
This is fine. 

This means that `bar` is a friend of `foo`, regardless of `bar`'s template arguments. 
`bar<char>`, `bar<int>, bar<float>` and any other `bar` would be friends of `foo<char>`. IDK what this is, but it's not **one-to-one**. 

If we wanted the **one-to-one**, then we would have to ensure that they are friends, only if the type that the template is argued to be, is the same. 

```
template <typename T> 
struct foo{ 
	friend class bar<T>;
};
```