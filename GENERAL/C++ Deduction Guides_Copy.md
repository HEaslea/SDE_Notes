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
`template <typename T> S(T) -> S<T>;` In my head, reads as, S, when constructed with type T, should be deduced as S<T>. 

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

