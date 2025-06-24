### `const`
For variables to be immutable. 

These can be evaluated at either **runtime** or **compile-time**, depending on how it's used. 

`const int x = 5; // cannot be changed later, will throw error`

Can't be used in call contexts that require compile time constraints (eg. array sizes, before C++11). 

### `constexpr`
C++11 introduced - improved over time. 

Means **the value is guaranteed, to be known at compile-time**, if possible. 

This can be used for more than variables, such as: 
- **Variables**
- **Functionds**
- **Constructors**

```
constexpr int square(int x)
{ 
	return x * x;
}

constexpr int result = square(5); // evaluated at compile time
```

These functions will still function as regular functions - if they are given non-const arguments - as in, arguments that are not evaluated at compile-time. 

### `consteval`
Introduced most recently, in C++ 20. 

If something is `consteval` - **it must be evaluated at compile time** : **no exceptions**. 

```
consteval int cube(int x) 
{
	return x * x * x;
}

int main() 
{ 
	constexpr int val = cube(3); // FINE
	int x = 3; 
	int y = cube(x); // XXXXX NOPE XXXXX is not a constant expression
}
```
If used at run time, this will throw an error. 

