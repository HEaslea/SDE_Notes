Arguments are passed to and values returned from. They can be overloaded, using the same name for several different functions. 
Pointers to functions as well at the end of the chapter. 

A FUNCTION is a block of code with a name. 

The function definition consists of a return type, a name, a list of zero or more parameters, and a body. 

The function body, is the statement block. 

Call operator, which is a pair of parentheses, with a comma separated list of arguments inside. 

### Writing one
We use the factorial example here: 
```
// factorial of val is val * (val - 1) * (val - 2) ..... 
int fact(int val){ 
	int ret = 1; // local variable to hodl the result as we calculate it
	while (val > 1){ 
		ret *= val--;
		}
	return ret; // returning the result
}
```


### Calling a function
`fact(n);` where n is an int that we pass to the function. 
This call does two things; initializes the function's parameters from the corresponding arguments, and it transfers control to that function. 

The function does begin with the (implicit) definition and initialization of its parameters. 
Therefore the first thing that happens that the int val is created. 
`return` will also pass control back to the main function (back to main) and away from the called function. 

### Parameters and Arguments
We would have no guarantee as to which order the arguments are evaluated in. 
The compiler is free to eval in whatever order it prefers. 

Argument must have matching type of the parameter in the function definition, there must be the same number of arguments as parameters. 
We couldn't do something like `fact("Hello")` duh, as we cannot convert a string to an int. 
However we could do something like this `fact(3.14)`. 
This would be implicitly converting the float to an int. 

### Parameter List 
For an empty list, you can leave the brackets empty, or you can use `void` like in C. 
Type definitions must be repeated, even if the types of two parameters are the same: 
```
int f3(int v1, int v2); This is how you do it. 
```

Outer scope, that which you call the function in, must not have the same name as the parameters. 

### Return Types
`void` indicates that we do not return a type. 
The return type can also not be an array type, or a function type (which is something that you can do in python I believe). 
However, we can return a pointer to an array, and a function too. 

## Local Objects
Names have scopes, objects have **lifetimes**. 
- The scope of a name is the part of the program's text in which that name is visible. 
- The lifetime of an object is the time during the program's execution that the object exists. 
The block of a function is the statement block, it has its own scope (a new scope) in which we can define variables. 
Parameters and variables defined inside a function body are referred to as local variables. They are "local" to that function and hide declarations of the same name made in outer scope. 

### Automatic Objects
Objects that correspond to ordinary local variables are created when the function's control path passes through the variable's definition. 
They are destroyed when the control passes through the end of the block in which the variable is defined. 
Objects that exist only while the block is executing are known as automatic objects. 

When we exit the block, the values of these objects created in that block are undefined. 

Parameters are automatic objects. Storage for them is allocated when the function begins. They are defined in scope. They are DESTROYED when the function terminates. 

They are initialized, with the function's arguments. 


## Local Static Objects
It can be useful to have a local variable whose lifetime continues across calls to the function. 
We make such objects by defining a local variable as `static`. 
They are not destroyed when the function ends, they are destroyed when the program terminates. They are initialized before the first time execution passes through the object's definition. 
[[Static Keyword]]

As a trivial example, counting how many times the function is called;
```
size_t count_calls(){ 
	static size_t ctr = 0; // value will persist across calls
	return ++ctr; 
}
int main(){ 
	for (size_t i = 0; i != 10; ++i){ 
		cout << count_calls() << endl;
		}
}
```
Will print 1 through 10 inclusive. `ctr` is already defined and given the initial value of 0. 
If given no value to initialize with, a static value will be given 0. 

[[Function Declarations]]