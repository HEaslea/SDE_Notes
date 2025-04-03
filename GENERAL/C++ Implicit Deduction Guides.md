They are automatically generated deduction guides, by the compiler, in certain situations, to help with template argument deduction. 
Along with CTAD, class template argument deduction, together, with the implicit deduction guides, allows the compiler to basically do whatever the fuck it wants and deduce based on constructor arguments.

When we create a class template and provide constructors, the compiler can generate implicit deduction guides based on those constructors that we have. 
The guides help the compiler deduce the template parameters based on the types of the arguments passed to the constructor. 

```
template <typename T> 
struct Box 
{ 
	T value;
	Box(T v) : value(v) {}
};

Box b(42); // Template parameter implicitly deduced 
```

It will produce it by taking the argument types from the constructors of the template. 
For each constructor, the compiler will try to deduce template parameters based on the constructors parameter types. 

```
template <typename T> 
struct Pair 
{ 
	T first, second;
	Pair(T a, T b) : first(a), second(b) {}
	Pair(T a) : first(a), second(T{}) {}
};

int main() 
{ 
	Pair p1(1, 2); // T is deduced as int
	Pair p2(3.14); // T is deduced as int 
	// with second arg default value initialized
}
```
 For these, these are the generated deduction guides from the compiler: 
 ```
 Pair(int, int) -> Pair<int>;
 Pair(double) -> Pair<double>;
```
### When are they Generated
- The class template has at least one constructor: 
- The constructor parameters can be used to deduce the template arguments.

### When They are NOT Generated
- There are no constructors in order to generate deduction guides. 
- The constructor's parameters do not provide enough information for the compiler to deduce the template parameters. 
- The template parameters are no deductible just from the constructor arguments. 
- If our class has complicated constructors that may use SFINAE, the compiler might not be able to generate meaningful implicit deduction guides. 

### Quick Example
```
template <typename T> 
struct Wrapper
{ 
	T value;
	Wrapper(T v) : value(v) {}
};

int main() 
{ 
	Wrapper w1(42); // T is deduced as int
	Wrapper w2(3.14); // T as double
	Wrapper w3("hello"); // NOT STD::STRING, const char* instead.
}
```

#### Key To Remember
When we are writing our own libraries, we might query whether we want to allow the fact that we can use just constructors to deduce the template types. 
We might have to consider, for each constructor, whether we want to offer it as a source for implicit deduction guides. 
Otherwise you have to do some weird shit in order to avoid it. 
Beware of silent productions of misbehaving instantiations. 
#### Injected Class Names