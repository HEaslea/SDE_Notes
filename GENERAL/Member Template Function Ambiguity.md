Similar to typename, when we have a templated class/struct, we have some ambiguity for our compiler, whether something is type/typename or a member variable. 
You would imagine that if we have something similar with a templated member function within a templated class. 

```
template <typename T> 
struct Container 
{ 
	template<typename U> 
	void doSomething(U val)
	{ 
		std::cout << "Value : " << val << std::endl;
	}
};

template<typename U> 
void exampleFunction(Container<U>& c)
{ 
	c.template doSomething<int>(42);
}

int main()
{ 
	Container<double> cont; 
	exampleFunction(cont);
}
```

The overarching issue here is that `Container` is a templated object as well. 
Similar to `typename`, the compiler will not be sure whether we have a data member of: 
- A data member of type `doSomething<int>` or 
- A member function template `doSomething<T>`

If we were to write something like this: 
`obj.doSomething(42)` The type of `obj` obviously depends on what we passed to the `Container` template, therefore, our type here is `obj<T>`. 
The compiler doesn't yet know what T is. 

The thing you have to really view here is that we are passing into another function, and when we are writing the code, is there ambiguity in what could be said, imagine that `Container<T>` had a data member. 

You may think that the `()` would give away that it is a function. However, there is a subtlety in the `template instantiation phase`. 

We have to think that the meaning of `doSomething` in general, has everything to do with `<T>`. 
During the compiler's parsing phase, this () doesn't really do a whole lot for it. 
This is the case when we think about having a data member that might be a callable object as well, so how would we know if it is a member variable or a template member function. 

T is unknown at time of parsing, so really we are just explaining/ giving a bit more information to our parser to understand, "you want to check this, without every piece of information, you might need this to understand". 
The final is that the **compiler cannot say FOR SURE whether it's a template function or not**. 
Under this ambiguous scenario, the compiler will tend to go and treat this as a member variable, unless explicitly told not to. 

The main bit of confusion, was thinking that member variables are unlike functions in all ways: 
however think about this as an example: 
```
struct Callable 
{ 
	void operator()(int val)
	{ 
		// do something
	}
};

template<typename T> 
struct Example
{ 
	T doSomething; // this could be an object with the operator()
};
```

Starting to see how variables can be really ambiguous, we might as well just state. 

Another basic example: 
```
template <typename T> 
class Container 
{ 
public: 
	template<typename U> 
	void show(U val)
	{ 
		cout << "Value : " << val << endl;
	}
};

template<typename T> 
void process(Container<T>& c)
{ 
	c.show(42);
}

int main()
{ 
	Container<int> c; 
	process(c);

	return 0;
}
```

The code won't compile on the basis that the compiler will not be able to figure out if `show` here is a member template or a function or some other entity (like a data member or regular function). 

Therefore: 
```
template<typename T> 
void process(Container<T>& c)
{ 
	c.template show(42); // then there's int deduction
}
```

This is only needed when we have to say that we are using a template member function, we don't need it for much else at all. 
- When you call a **member function template** of a **template class** where the class type depends on a template parameter.
- When using templates inside template classes where dependent types and names require disambiguation.