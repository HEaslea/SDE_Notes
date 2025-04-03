[[BtB Templates]]
Had to use replit here, as my compiler isn't updated for concepts. 

#### Creating our own Concepts
```
template <typename T> 
concept Addable = requires(T a, T b){ 
	{a + b}-> std::same_as<T>; // addition operation must return the same type.
}
```

Then we have to set this to be used in a template for something else later on: 

```
template<typename T> 
void add_and_print(const T& a, const T& b) requires Addable<T> { 
	cout << "Sum: " << (a + b) << endl;
}
```

We could make sure that something is `cout'able` 
```
template<typename T> 
concept Coutable = requires(T t){ 
	{std::cout << t}; // expression to check if T is cout'able
};
```

Concept is like a "Class" in that we have to `;` at the end. 
It's also like a function in that we have the body of the concept; 
in the body of the concept we have the expression that we want to check. It can be thought of as a predicate. 

```
template<typname T, typename U> 
concept Subtractable = requires(T t, U u){ 
	{ t - u }; // expression to check if T - U is valid
};

template<Subtractable T, Subtractable U> 
auto Subtact(T t, U u){ 
	return t - u;
}
```



```
template <typename T> 
concept Addable = requires(T a, T b){ 
	{ a + b } -> std::same_as<T>; 
};
```

```
template <typename T>
concept StreamableToCout = requries(const T& t) { 
	{ std::cout << t } -> std::convertible_to<std::ostream&>;
};
```
the `std::convertible_to` here means that the expression can be converted to `std::ostream&`. 

The `->` refers to the return type. 

