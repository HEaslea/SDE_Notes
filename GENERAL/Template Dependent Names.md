### `.template`

```
template <typename T> 
class Wrapper 
{ 
public: 
	template <typename U> 
	void doSomething(U u)
	{ 
		// imp
	}
};

template <typename T> 
class User 
{ 
public:
	Wrapper<T> w;
	
	void execute()
	{ 
		// without .template here
		// the compiler wouldn't know if doSomething is a template
		w.template doSomething<int>(5);
	}
};
```
The compiler will not know what `Wrapper<T>` contains because at the time of writing and first pass, it hasn't been instantiated yet, I think that's what most of this type of code boils down to. And why we use `typename` as well. 
Otherwise the compiler may misinterpret the `<` as a `less_than` operator. 

```
template <typename T> 
class Container 
{ 
public: 
	template <typename U> 
	void add(U u)
	{ 
		... 
	}
};

template <typename T> 
class Manager 
{ 
public: 
	Container<T>* c;

	void insert() 
	{ 
		c->template add<double>(3.14);
	}
};
```
Think about what we can ascertain from the situation: 
`Container<T>* c`, `c` is a variable that is a pointer to a template class. 
The main trouble that we might run into here, is that the `<` just sort of comes out of left field when we `->` operator `c`. 
If we try and read it, from the spirit that we have never seen this code before, we have no idea what else is going on in other classes, then we might run into this line of thinking: 
> `c` is a pointer to a class that is a template, fine. Then when I say, `c->` we are accessing the object that `c` points to, fine. Then say we don't have the template and we just have `c->add<double>`. Before we really see the double, we are going bit by bit into this, and we get `c->add`, ok fine, this so far, could be a function, seems more like a variable, however, there is ambiguity here. Then we add `<double` well then, what are we looking at here, well that could be a `variableA < variableB`, which makes a lot of sense here. However, if we know that add is a template function, we go, "What's the confusion".  However, it seems pretty obvious when we go bit by bit, without taking too much for granted. 

Therefore if we have `.template` then it is explicitly clear what we are trying to use, the template function `add` instantiated with `<double>` and using the parm args of `(3.14)`. 

Without it, we may be looking at a template variable, `add<double>` , that is constructed using `3.14`. 

Really the only time that this is important, is when we are accessing a template member of a dependent type within a template. 

```
template <typename T> 
class Engine 
{ 
public: 
	template <typename U> 
	void start(U fuel)
	{ 
		// start engine with fuel
	}
};

template <typename T> 
class Car
{ 
public: 
	Engine<T> engine; 
	
	void ignite()
	{ 
		// correct usage with .template
		engine.template start<std::string>("Petrol");
	}
};
```

Here there is ambiguity in the sense that `Engine` is a template, and has a template function, therefore we will have to specify here. 


