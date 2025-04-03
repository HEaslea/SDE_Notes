The benefits of defining a function for such a small operation include the following: 
- Easier to read and understand a call to `shorterString` than it would be to read and understand the equivalent conditional expression. 
- Using a function ensures uniform behaviour. Each test is guaranteed to be done the same way. 
- If we need to change the computation, it is easier to change the function than to find and change every occurrence of the equivalent expression. 
- The function can be reused rather than rewritten for other applications. 

Calling a function is actually slower than the equivalent expression. 
On a normal machine, the function call does a lot of work: Registers are saved before the call and restored after the return; arguments may be copied; and the program branches to a new location. 

#### `inline` Functions avoid Function Call Overhead
Literally just means that it is expanded `in line` on call. 

Something as simple as `shorterString` might look something like this: 
```
// without
cout << shorterString(s1, s2) << endl;
cout << (s1.sizew() < s2.size() ? s1 : s2) << endl;
```

Like a macro, just puts in where that inline is. 

The RUN-TIME overhead of making `shorterString` a function is thus removed. 

Just as simple as
```
// inline version: finding the shorter of the two strings
inline const string&
shorterString(const string &s1, const string &s2){ 
	return s1.size() < s2.size() ? s1 : s2;
}
```
Funnily the compiler might actually just ignore the request to make it inline, it can literally just ignore it lol.

Many compilers might refuse to inline a recursive function. 
A 75-line function will almost surely not be expanded inline. 

#### `constexpr` Functions
These functions can be used in a constant expression. 
Defined like any other function. It must meet certain requirements. 
The `return` type and the type of each parameter in must be a literal type, and the function body can only contain one `return` statement: 
```
constexpr int new_sz() { return 42; }
constexpr int foo = new_sz(); // ok: foo is a constant expression
```

Remember that `constexpr` indicates that the value or return value is constant and where possible, is computed at compile time. 

`new_sz` being a `constexpr` takes no arguments. 
The compiler can verify at compile-time, that a call to `new_sz` returns a constant expression. 

When the compiler can, it will replace `constexpr` with the resulting value. 
The function body of a `constexpr` may contain other statements, however, so long as those statements generate no actions at run time. 
eg, they may contain null statements, type aliases, and using declarations. 

A `constexpr` function is permitted to return a value that is not constant. 
```
// scale(arg) is a constant expresison if arg is a constant expression 
constexpr size_t scale(size_t cnt) { return new_sz()* cnt;}
```

The scale function will return a constant expression if its arguments is a constant expression but not otherwise. 

Makes sense. 

```
int arr[scale(2)]; // ok : scale(2) is a constant expression
int i = 2; 
int a2[scale(i)]; // ERROR: scale(i) is not a constant expression. 
```

In the case of arguing into it an `int` literal,  then the compiler can realise the `constexpr` nature of this, and replace the call to `scale` with the resulting value. 

`inline` and `constexpr` are normally defined in header functions. 

