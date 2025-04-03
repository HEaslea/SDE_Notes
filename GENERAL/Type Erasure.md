Enables using various concrete types through a single generic interface. 

`void*`: 
eg. 

`int cmp(const void* a, const void* b)`

## Object Orientation
This is quite an obvious one: 
```
// all necessary incldues


struct BaseClass 
{ 
	virtual std::string getName() const = 0; // pure virtual for const objs
};

struct Bar : BaseClass 
{ 
	std::string getName() const override 
	{ 
		return "BAR";
	}
};

struct Foo : BaseClass 
{ 
	std::string getName() const override 
	{ 
		return "FOO";
	}
};

void printName(std::vector<const BaseClass*> vec)
{ 
	for(auto v : vec)
	{ 
		// with dynamic dispatch so we get dyanmic type at run time
		std::cout << v->getName() << std::endl;
	}
}

int main() 
{ 
	Foo foo; 
	Bar bar;

	std::vector<const BaseClass*> vec(&foo, &bar);
	printName(vec);
}
```

Really really basic: 
`BaseClass` is an abstract class here. 
`Foo` and `Bar` implement the interface of `BaseClass` here. 

In such a language like Python, you are more interested in behaviour, and not implementing interfaces etc. 

## Templates: 
The idea in python, that we care more about behaviour, and less about interfaces, is known as duck typing: 
The idea being 
> "When I see a bird that walks like a duck, swims like a duck and quacks like a duck, I call that bird a duck"

Here's an example, in C++, if a function takes in `Duck`, then only ducks and derived types thereof, can be passed into the function. 
However in python, we can pass in all birds that behave like `Duck`.

However, thanks to templates, we have a form of duck typing in C++: HOORAY: 
When you combine this style of typing, with OO then you get Type_safe. 

`std::function` is a polymorphic function wrapper: 
Taking in anything that behaves like a function, ie, any casllable such as a function, function obejct, a function object created by `std::bind`, or just a lambda function. 

```
double add(double a, double b)
{ 
	return a + b;
}

struct Sub 
{ 
	double operator() (double a, double b)
	{ 
		return a - b;
	}
};

double multThree(double a, double b, double c)
{ 
	return a * b * c;
}

int main() 
{ 
	using namespace std::placeholders;

	std::map<const char, std::function<double(double, double)>> dispTable
	{ 
		{'+', add},
		{'-', Sub()},
		{'*', std::bind{mulThree, 1, _1, _2}},
		{'/', []{double a, double b} { return a / b ; }}
	};

	// usage
	cotu << "3.5 + 4.5 = " << dispTable['+'](3.5, 4.5) << endl;
}
```

Here the key thing is that `std::function` is accepting of all different function types and erases their types. The only requirement here is that it takes two doubles and returns a double. 
`std::function<double(double, double)>`


![[Pasted image 20240917190000.png]]

#### Type Erasure with OO
This type of type erasure sort of boils down to inheritance heirarchy: 
Here we have type safety
and it's easy to implement. 
However, we have virtual dispatch, somewhat intrusive, as the derived class must know about its base. 


### With Templates
```
class Object 
{ 
public: 
	template <typename T> // now this is interesting
	object(T&& obj) : object(std::make_shared<Model<T>>(std::forward<T>(obj))) {}
	
	std::string getName() const 
	{ 
		return object->getName();
	}
	
	struct Concept
	{ 
		virtual ~Concept() {}
		vitual std::string getName() const = 0;
	}

	template<typename T> 
	struct Model : Concept
	{ 
		Model(const T& t) ; object(t) { }
		std::string getName() const override 
		{ 
			return object.getName();
		}
	private: 
		T object;
	}
	
	std::shared_ptr<const Concept> object;
};

void printName(std::vector<Object> vec)
{ 
	for(auto v : vec) std::cout << v.getName() << endl;
}

struct Bar
{ 
	std::string getName() const 
	{ 
		return "BAR";
	}
};

struct Foo
{ 
	std::string getName() const 
	{ 
		return "FOO";
	}
};

int main() 
{
	std::vector<Object> vec{ Object(Foo()), Object(Bar())};
	printName(vec);
}
```

Notice here that the vector can only be homogeneous, taking in of one type only, we do not disrupt that here. 
It's almost as if we are layering and wrapping about the thing that will allow us to create this polymorphic nature, the `std::shared_ptr`
Object is wrapping `Concept` which is the base of `Model`, therefore, object can have the pointer to a concept, and then we can access the polymorphic values thsi way. 

So we still have some virtual dynamic dispatch going on here, however, it's all wrapped up, in a nice object package. 



The normal follow on here is:
#### std::any
This basically does what we would want:  here is a possible implementation
```
class Any 
{ 
	struct BaseHolder
	{ 
		virtual ~BaseHolder() = default;
		virtual const std::type_info& type() const = 0;
		virtual std::unique_ptr<BaseHolder> clone() const = 0;
	};

	template<typename T> 
	struct Holder : BaseHolder 
	{ 
		T value; 

		Holder(const T& v) : value(v) {} // for lvalues
		Holder(T&& v) : value(std::move(v)) {} // predominanty for rvalues

		const std::type_info& type() const override
		{ 
			return typeid(T);
		}
		
		std::unique_ptr<BaseHOlder> clone() const override
		{ 
			return std::make_unique<Holder<T>>(value);
		}
	};

	std::unique_ptr<BaseHolder> content;

public: 
	Any() = default;

	template <typename T>
	Any(T&& value)
		: content(std::make_unique<Holder<std::decay_t<T>>>(std::forward<T>(value))) {}

	Any(const Any& other) 
	: content(other.content? other.content->clone() : nullptr) {}

	Any(Any&& other) noexcept = default;

	//Copy Assignment operator
	Any& operator=(const Any& other) 
	{ 
		if(this != &other)
		{ 
			content = other.content? other.conent->clone() : nullptr;
		}
		return *this;
	}

	// move assignment operator
	Any& operator=(Any&& other) noexcept = default;

	bool has_value() const 
	{ 
		return content != nullptr;
	}

	// returning the type of the stored value
	const std::type_info& type() const 
	{ 
		return content ? content->type() : typeid(void);
	}

	void reset() 
	{ 
		content.reset();
	}

	template<typename T> 
	T& any_cast() 
	{ 
		if(typeid(T) != content->type())
		{ 
			throw std::bad_cast();
		}
	}
};
```

In this sense `Any` is a holder of `BaseHolder` which is an private abstract class. Then we have `Holder`, which takes in any type, and inherits from `BaseHolder`, and is a derived template class. 
The `Any` class itself holds the `std::unique_ptr` to the `BaseHolder`. 
