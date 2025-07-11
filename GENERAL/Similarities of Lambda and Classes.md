Lambdas generate function objects. 

That means that they have defined in them the `operator()()` function. 

These classes are of course structured in a certain way: 
- the class only consists of one member function (the `operator()()`I believe). And this member function will have `const` on it. 
- the capture clause is a combination of the class' member variables its constructor

### Capture By Value

```
auto x = 3; 
auto is_above = [x] (int y) { 
	return y > x; 
};

auto test = is_above(5);
```

This will correspond to the class: 
```
auto x = 3; 

class IsAbove{ 
	IsAbove(int x) : x{x} {}
	auto operator()(int y) const // please notice the const here
	{ 
		return y > x;
	}
private: 
	int x{}; // value
}

// then the same as before
auto is_above = IsAbove{x};
auto test = is_above(5);
```

### Capture by Reference

```
auto x = 3
auto is_above = [&x](int y ){ return y > x; };
auto test = is_above(5);
```

This will correspond to this class: 
```
auto x = 3; 

class IsAbove{ 
public:
	IsAbove(int& x) : x{x} {} // notice the referenc here
	
	auto operator()(int y) { return y > x; }
	
private: 
	int& x; // reference
};
```

#### Initializing Variables in Capture
```
auto some_func = [numbers = std::list<int>{4, 2}] () { 
	for(auto i : numbers)
		std::cout << i; 
}
```

This will correspond to this class: 
```
class SomeFunc{ 
public: 
	someFunc() : numbers{4, 2} {}
	void operator()()const { 
		for(auto i : numbers)
			std::cout << i;
	}

private: 
	std::list<int> numbers;
};
```

#### Mutable Member Variables
`auto counter_func = [counter = 1]() mutable {...};`
If we are capturing by reference, then we don't need to add this `mutable` idea. 

### Without Captures and C style function pointers
If there are no captures in the lambda, the class really is just a function `operator()()`. 
This might look a little like this: 
```
extern void download_webpage(const char* url, void(*callback)(int, const char*));
```

This will need to have the `+` in order to have it decay into a function pointer. 
Here is an idea using `qsort`

```
std::qsort(arr, 3, sizeof(int), +[](const void* a, const void* b) { 
	return *(int*) a - *(int*)b;
}
```

We would do well to remember that Lambdas without captures can be default constructible, and assignable very easily. 
However, lambdas with captures will have their own type. 

Lambdas with captures are called Stateful Lambdas. 

To be able to pass around lambdas with captures, use `std::function`. 

### Lambdas and `std::function`
So that we can pass around stateful lambdas. 

We can use `std::function` to enclose a lambda expression. 

`std::function` that works somewhat like this: 

`std::function< return_type (parameter0, parameter1 ...)>`, eg. `std::function< void (void)>`. 

`auto func = std::function<bool(int, std::string)>`

The capture of a lambda does not affect its signature, therefore, no capture will change the `std::function` that will enclose the lambda. 

```
class Button{ 
public: 
	Button(std::function<void(void)> click) : handler(click) {}

	auto on_click() const { handler(); }
	
private: 
	std::function<void (void)> handler{}
};
```

```
auto create_buttons() { 
	auto beep = Button([counter = 0]() mutable { 
		std::cout << "Beep: " << counter << "!";
		++counter;
	})
}
```

We need the `mutable` here, so that we can change the `counter` that we see in the lambda that is captured. 

Remember that we can wrap lambdas with the same signature, no matter what they capture. Changing the capture does not change the function signature. 

### Generic Lambdas
Using `auto`: 
```
auto v = 3; // int
auto lambda = [v](auto v0, auto v1) { 
	return v + v0*v1;
}
```

This will be translated as: 
```
class Lambda { 
public: 
	Lambda(int v) : v(v) {}
	
	template <typename T0, typename T1> 
	auto operator()(T0 v0, T1 v1) const 
	{ 
		return v + v0*v1;
	}
	
private: 
	int v{};
};
```

