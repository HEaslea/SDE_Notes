#functionmatching
Consider the following: 
```
void f(); 
void f(int);
void f(int, int);
void f(double, double = 3.14);
f(5.6); // this would call f(double,double)
```
However, it is not that easy to see that this is going to happen right off the bat. 

### Determining the Candidate and Viable Functions
The first step to function matching is identifying the set of overloaded functions considered for the call. 
They would be called candidate functions. 
They have the same name as the called function and for which a declaration is visible at the point of the call. 
There would be 4 candidate functions here. 
Then we select those that can be called through the arguments that are given. 

These are called the viable functions. 
This means that same number of parameters, as there are arguments in the call, and the type of each argument must match too, of be convertible to the type of its corresponding parameter. 

Elimination occurring here. 
The function taking two `doubles` can be called with a single argument, as it has a default parameter. 

THIS IS CRUCIAL, a function with default parameters may be called with fewer arguments then we might think. 

After that, we look at whether the argument types match those of the parameters. 
As with any call, an argument might match its parameter either because the types match exactly or because there is a conversion. Matching or Conversion. 

Then we see that in our example we would have two viable functions: 
```
1. f(int)
2. f(double, double)
```
1. is viable because a conversion exists that can convert the argument of type `double` to the parameter of type `int`
2. the second is viable because the default parameter is provided for the function's second parameter, and its first parameter is of type double, which exactly matches the type of the argument in the call

If there are no viable functions, then the compiler whines that we have no matching functions.

Then we have to see which of the functions provides the best match for the call. 

The process looks at each argument in the call and selects the viable function for which the corresponding parameter best matches that argument. 

The closer the types match, the "better". 

The `f(double, double)`, in short, doesn't require a conversion to type int, that would match the `f(int)` call. 
The exact match, working better than the conversion. 
Therefore `f(5.6)` 


HOWEVER, what would happen if we passed something like `f(42, 2.56)`. 
See that the compiler will select those functions that have the required number of parameters and for which the argument types match the parameter types. 
In this case, the viable functions are `f(int, int)` and `f(double, double)` again.
Then we determine which function is the best match again. 
There is an "overall best", match if there is one and only one function for which: 
 - The match for each argument is no worse than the match required by any other viable function. 
 - There is at least one argument for which the match is better than the match provided by any other viable function. 
With this, there is no single function that is preferable, then the call is in error. 
The compiler will complain that the call is ambiguous, lol. Here lies the issue. 

If, here, we look at the first argument, then we might say that `f(int, int)` is better. As in order to call `f(double, double)` the first `int` would have to be converted to a double. 

Which is weirdly considered "less good", than one that is exact. 
However, the second argument would suggest that `f(double, double)` is the better match. 

The compiler will reject this call on the basis that it is ambiguous. 

Argument casts are a thing, however, they shouldn't be necessary. 


### Argument Type Conversions
The compiler will rank the conversion that could be used to convert each argument to the type of its corresponding parameter. Conversions are ranked as followed: 
1. An Exact Match . This happens when: - The argument and parameter are identical types: - the argument is converted from an array or function type to the corresponding pointer type: - A top-level `const` is added to or discarded from the argument.
2. Match through a `const` conversion
3. Match through a promotion
4. Match through an arithmetic or pointer conversion. 
5. Match through a class-type conversion, covered later. 

I mean most of these should be avoided, your functions should be written well enough to where this is minimized. 

Remember this: the small integral types always promote to int or to a larger integral type. 
Given two functions, one of which takes an int and the other a short, the short version will be called only on values of type `short`. 
Even those values seem a closer match, those values will be promoted to an `int`, whereas calling the `short` version would require a conversion: 
```
void ff(int); 
void ff(short); 
ff('a'); // char promotes to int; calls f(int); 
```
Seems really obvious tbh. 

All the arithmetic conversions are treated as equivalent to each other. 
The conversion from `int` to `unsigned int`, does not take precedence over the conversion from `int` to `double`. 
```
void manip(long); 
void manip(float);

manip(3.14); // error; ambiguous call
```
The literal 3.14 is a `double`. That type can be converted to either `long` or `float`. They are the same conversions, both arithmetic, in the compiler's eyes. Therefore, a compiler error due to ambiguous call. 

Then there is constness: 
```
Record lookup(Account&); // function that takes a reference to Account
Record lookup(const Account&);// new function that takes a const reference
const Account a; 
Account b; 
lookup(a); // calls lookup(const Account&);
lookup(b); // calls lookup(Account&);
```
Seems super obvious. 

We cannot bind a plain reference to a `const` object. Moreover, that call is an exact match to the argument a. 
`lookup(b), into Record lookup(const Account&)`, now this can be both, however, it will require a conversion. 
The `nonconst` parameter is an exact match. 

The same thing will happen with pointers too. 

