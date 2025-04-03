Letting the compiler know, how we want to settle a deduction. 

```
template <typename T> 
struct S
{ 
	T a;
};

// deduction guides
// go onto 314 and 288
template <typename T> S(T) -> S<T>;

S x{12}; // S<int> x{12};
S y(12); // S<int> y(12);
auto z = S{12}; Same as auto z = S<int>{12};
```

The deduction guide, reads to me like, "When I pass `S(T)`, I want it deduced as `S<T>` ". 

The deduction looks a little like a function template: 
We say that we are defining a template something, then we say what we are providing, and then how that should be deduced. 
The guided type, which is after the `->`, must be a template id. 
It can also have the explicit specifier. 
The `S` in the declarations, are `placehohlder class type`. 
The idea is that when we use `S`, we then have to have the variable names, then the initializer, any other way, just won't work: 
`S* p = &x; // ERROR Syntax not Permitted`. 
When we use `S x(12)`, the deduction guides are treated as an overloaded set. 
Attempting overload resolution. 
Here we have one in that set, therefore we induce the guide's guided type to be `S<int>`. 
If we were to do something like this: 
`sS s1(1), s2(2.0); // ERROR` This would result in an error as the S's types differ here. 
Similar to when we have `auto A{12}, B{1.2}; // ERROR`. 

This works really well for aggregated sets: 
However, we don't need this if we have a constructor that takes in the type that can be deduced: 

```
template <typename T> 
struct B
{ 
	B(T a) : _a(a) {}
	T _a;
};

B a(1);
B b{2};
```
These are both fine. 
However, if we were to do something like this: 
`std::vector vi = {1, 2, 3}, vd = {1.1, 1.2, 1.3};`. 
This would result in an error. 

I have never realised this about aggregate structs:
![[Pasted image 20241024011930.png]]
![[Pasted image 20241024011939.png]]

This is the reality of the situation 
Notice that in the copy initialization that `T object = {other};` Was a copy until C++ 11. 
That means that copy elision, not just initializes T's members, memberwise. 
Notice as well, when we throw an object, we are copying as well. 

![[Pasted image 20241024012225.png]]
This means, that the last will be in error, as there are no constructors that can do this for us, as this is an aggregate struct. 
This is because aggregate initialization is only allowed in specific forms. 
Therefore `S c(3)` does not work as it's not aggregate initialization. 
It is trying to use direct initialization, which would call a constructor, `S`wasn't even given a compile generated one. 
This means that `S b = {2};` has to use copy elision, as there is no copying going on at all, this is a great way to show that. 

We are able to deduce when we have some constructors: 
```
template <typename T, typename U> 
class MyPair
{ 
public: 
	MyPair(T t, U u) : first(t), second(u) {}
	T first;
	U second;

	void outType()
	{ 
		cout << typeid(T).name() << "    " << typeid(U).name() << endl;
	}
};

MyPair(const char*, int) -> MyPair<std::string, int>;

int main() 
{ 
	MyPair a(1, 2.1); // will out i : d
	MyPair b("Hello There", 1);
}
```

This is fine, and we can use this `a`. 
In the `b`, what would be `const char*`, is actually `std::string`. 

We might be able to do this as well: 
`Pair(int) -> Pair<int, int>;`

These are really good when we have a factory function that we want to have work something like this: 
```
template <typename T> 
class Widget 
{ 
public: 
	Widget(T value) {}
};

template <typename T> 
Widget<T> make_widget(T value)
{ 
	return Widget<T>(value);
}

// Deduction Guide
Widget(int) -> Widget<int>;
```

