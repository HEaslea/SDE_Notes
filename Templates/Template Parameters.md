A template parameter name has no intrinsic meaning: 
We can use any name, mostly we use `T`. 
```
template <typename Foo>
```

###### Template Parameters and Scope
Follow normal scoping rules. 
As with any other name, the template parameter hides any declaration of that name in an outer scope. 
A name used as a template parameter may not be reused within the template itself: 
```
typedef double A; 
template<typname A, typename B> void f(A a, B b)
{ 
	A tmp = a; // tmp has same type as the template parameter A, not double, not visible in here, hiding outer typedef

	double B; // error as B is already a template parameter in here
}
```

The param name can only appear once obviously: 
`template <typename V, typename V>` no go. 

##### Template Declarations
Declaration must include the template params: 
```
// declares without defining compare and Blob
template <typename T> int compare(const T&, const T&);
template <typename T> class Blob:
```
Names from declaration to definition need not be the same: 
```
// all three refer to the same funciton template
template <typename T> T calc(const T&, const T&); // declaration

template <typename U> U calc(const U&, const U&); // declaration again


// definition of the template
template <typename Type> 
Type calc(const Type& a, const Type& b){ /* ... */ }
```
Each declaration must be given the same number and kind of parameters as per usual. 


###### Using Class Members that Are Types
Assuming `T` is a template parameter type, the compiler will look at `T::mem` and it won't know until instantiation time whether `mem` is a type or a `static` data member. 
To process the template, we have to know whether a name represents a type. 
Eg. assuming `T` is the name of a type parameter, when the compiler sees a statement of the following form: 
`T::size_type *p;` it will need to know whether we're defining a variable named `p` or are multiplying a `static` data member named `size_type` by a variable `p`. 
So are we defining, or multiplying, based on what `p` is. 

By default, when we use scope operators, the compiler assumes that it's not a type, but rather a variable, that we can use. 
If we want to use a type member of a template type parameter, we must explicitly tell the compiler that the name is a type. 

#typename
Therefore we have to use the `typename`: 
```
template <typename T> 
typename T:: value_type top(const T& c)
{ 
	if(!c.empty())
		return c.back()
	else 
		return typename T::value_type();
}
```
Using `typename` to specify the return type. 

> **When we are informing the compiler that a name represents a type, we must use the keyword `typename`, not `class`**

#### Default Template Arguments
We can supply default template arguments. 
For both class and function templates. 
 
```
// compare has a default tempalte argument, less<T>
// and a default function argument F()

template <typename T, typename F = less<T>>
int compare(const T& v1, const T& v2, F f = F())
{ 
	if(f(v1, v1)) return -1;
	if(f(v2, v0)) return 1;
	return 0;
}
```

- `typename F = less<T>` the type of a comparison function object. Defaulting to `std::less<T>`, which is a functor (any type implementing `operator()`)
- `F f= F()`: comparison function object, passed by value, default constructed to an instance of `F`(again defaulting to `std::less<T>`), this one will be comparing. It means that we are default initializing an object of type `F` to `f`. 

The `F` represents the type of a callable object. `f` will be bound to this callable object. 
The default template argument specifies that `compare` will use the library `less` function-object class, instantiated with the same type parameter as `compare`. 
The default function argument, says that `f` will be a default-initialized object of type `F`. 
Therefore `compare` callers, will not need to supply their own comparison operation:
```
bool i = compare(0,42); // uses less; i is -1

Sales_data item1(cin), ietm2(cin);
bool j = compare(item1, item2, compareIsbn);
```

In the first, our default function argument, default initialized to `less<T>`, here `T` is int so that object has type `less<int>`. 
In the second, the type of the third argument will have to be a callable object of some kind.

#### Template Default Arguments and Class Templates
So we have to use <> in order to denote that we are using a class with templates, if we want to use all the default parameters, then just supply the empty <>. 

```
template<class T = int> class Numbers{ 
	// by default T is int
public: 
	Numbers(T v = 0) : val(v) {}
	// works as a default constructor
	// due to default arguments given
	// various operations on numbers
private: 
	T val;
};

Numbers<long double> lots_of_precision;
Nubmers<> average_precision; // says we want to use default type, which is int
```

### Member Templates 
#membertemplates
Either a normal or template class can have a member function that is a template. 
They are known as "member templates". They may not be virtual either. 

##### Member Templates of Ordinary (non-template) Classes
```
// function-object class that calls delete on a given pointer
class DebugDelete{ 
public: 
	DebugDelete(std:ostream& s = std::cerr) : os(s) {}
	// considered the defalt ctor
	// as with any function template, the type of T
	// is deduced by the compiler
	template <typename T> void operator()(T* p) const{ 
		os << "Deleting unique_ptr " << std::endl;
		delete p;
	}
private: 
	std::ostream &os;
};
```

```
double *p = new double;
DebugDelete d; 
// and object that acts as a delete expressionco
d(p);
// calling DebugDelete::operator()(double*)
int *ip = new int;
// calls operator()(int*) on a temp DebugDelete object
DebugDelete()(ip);
```

Calling a `DebugDelete` object `delete`s its given pointer, we can also use `DebugDelete` as the "deleter" of a `unique_ptr`. 
To override the "deleter" of a `unique_ptr`, we supply the type of the deleter inside brackets and supply an object of the deleter type to the constructor: 
```
// destroying object to which p points
// instantiates DebugDelete::operator()<int>(int*)

unique_ptr<int, DebugDelete> p(new int, DebugDelete());
// passing in a default init'd DebugDelete

// destorying the object to which sp points
// instantiates DebugDelete::operator()<string> (string*)
unique_ptr<string, DebugDelete> sp(new string, DebugDelete())
```

The `unique_ptr` destructor will call the `DebugDelete`'s call operator. 


Remember that classes are just types that we are creating. 


Another thing to remember is the most vexing parse: 
```
// NOT CREATING a default init object of A 
A a(); // most vexing parse
A a; // this works for default init A object

// Using A a() will be reserved for when we want default ctor
// And we want to disambiguate between a function declaration and an object declaration: 

MyClass obj(); // Declaration of Function name
MyClass obj; // Declaration of a default-init MyClass obj

MyClass *ptr = new MyClass(); // default MyClass obj here

MyClass createObject() { 
	return MyClass(); // return default init MyClass obj
}
```
If we write `A a();` the compiler will generally think that we are declaring a function, rather than an object. 
We can write `A a{};`, that works, and is usually better and preferred for clarity and to avoid potential ambiguities. 

#### Member Templates of Class Templates
Giving `Blob` class a constructor that will take two iterators denoting a range of elements to copy. 
We'd like to support iterators into varying kinds of sequences, we'll make this constructor a template: 
```
template <typename T> class Blob{ 
	template <typename It> Blob(It b, It e);
	// ...
};
```

The constructor has its own template type parameter, `It`, which it uses for the type of its two function parameters. 

Member templates **ARE** function templates. 
If we want to define outside the class, the parameter list for the class template has to come first, followed by the member's own template parameter list: 
```
template<typename T> // class parameter list
template<typename It> // type parameter for constructor
	Blob<T>::Blob(It b, It e) : 
		data(std::make_shared<std::vector<T>> (b, e)) {}
```

Defining a member of a class template that has one template type parameter, which we have named `T`. 
The member itself is a function template that has a type parameter named `It`. 

Really, the class template's template has to come first, then we move to the function's parameter type. 
Then using `Blob<T>` to get into the scope of the class instantiated to the type `T`. 


#### Instantiation and Member Templates
To instantiate a member template of a class template, we must supply arguments for the template parameters of both the class and the function. 

Arguments for the class template are determined by the type of the object through which we call the member template. 

The compiler will typically deduce template arguments for the members own parameter from the arguments passed in the call. 

Remember that we don't have to give `<args>`, when the compiler can deduce them, remembering what we have read about in "modern C++". 
```
template<typename T> 
void PrintOut(T& container){ 
	for(auto i : container){ 
		cout << i << "  ";
	}
	cout << endl;
}
int main() { 
	vector<int> vi{0,1,2,3,4};
	PrintOut(vi);
	PrintOut<vector<int>>(vi);
	// both are fine here
}
```

Therefore, when we call a template member function, within a template class, we don't need to template arg `<arg>` in order to instantiate the type of member function that we are calling. However, we do need to `<arg>` the class template portion of it. 

```
int ia[] = {0,1,2,3,4,5,6,7,8,9};
vector<long> vi = {0,1,2,3,4,5,6,7,8,9};
list<const char*> w = {"now", "is", "the", "time"};

// instantiates the Blob<int> class
// and the Blob<int> constructor that has two int* parameters
Blob<int> a1(begin(ia), end(ia));

// instantiates the Blob<int> constructor that has
// two vector<long>::iterator paramters
Blob<int> a2(vi.begin(), vi.end());

// instantiates the Blob<string> class and Blob<string> 
// constructor that has two (list<const char*>::iterator parameters)
Blob<string> a3(w.begin(), w.end());
```

```
list<const char*> w = {/* ... */}; // list of c-style string
auto it = w.begin(); 
// it = std::list<const char*>::iterator
```

The constructor's own parameters will be deduced from the type of `begin(whatever), end(whatever)`. 
If we have `Blob<int>` instantiated once, then we can reuse it, changing what `It` is instantiated to. 


