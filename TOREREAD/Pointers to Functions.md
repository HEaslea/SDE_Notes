#pointerstofunctions

Pointers that denote functions rather than an object.

Now a function's type is determined by it return type and the types of its parameters. 

```
//compares length of two strings
bool lengthCompare(const string&, const string&);
```
To declare a pointer that can point at this function, we declare a pointer in place of the function name. 
```
// pf points to a function returning bool that takes two const string references
bool (*pf)(const string&, const string&); // uninitialized
```

Starting from the name, we are declaring, we see that `pf` is preceded by a `*`. 
So `pf` is a pointer. 
To the right is a parameter list, which means that `pf` points to a function, so it's that parameter list, that causes the idea of pointing to a function. 
Then to the left, the function returns is the `bool`. Therefore, `pf` points to a function that has two `const string&` parameters and returns `bool`. 

The parentheses around the `pf` is necessary. If we omit them then we declare pf as a function that returns a pointer to bool: 
```
// declares a function named pf that returns a bool*
bool *pf(const string&, const string&);
```

### Using Function Pointers
So when we use the name of a function as a value, the function is automatically converted to a pointer. 
This is just the name without the `()` calling.
We can assign the address of `lengthCompare` to `pf` as follows: 
```
pf = lengthCompare; // pf now points to the function named lengthCompare
pf = &lengthCompare; // equivalent assignment, address-of operator is optional
```

And then we can use this to call the function to which the pointer points. 
You don't need to dereference the pointer here weirdly: 
```
bool b1 = pf("hello", "goodbye"); // calls lengthCompare
bool b2 = (*pf)("hello", "goodbye"); // equivalent call
bol b3 = lengthCompare("hello", "goodbye"); // equivalent call
```

There is no conversion between pointers to one function type and pointers to another function type. 
We can assign `nullptr` or a zero-valued integer constant expression to a function pointer to indicate that the pointer does not point to any function: 

```
string::size_type sumLength(const string&, const string&); 
bool cstringCompare(const char*, const char*); 
pf = 0; // ok pf points to no function
pf = sumLength; // error; return type differs
pf = cstringCompare; // error: paramter types differ
pf = lengthCompare; // ok: function and pointer types match exactly
```

### Pointers to Overloaded Functions
#pointerstooverloadedfunctions

The context <u>must</u> make it clear to which function we are pointing to. 
```
void ff(int*);
void ff(unsigned int);
void (*pf1)(unsigned int) = ff; // pf1 points to ff(unsigned)
```

`void (*pf2)(int) = ff; // error: no ff with a matching parameter list`

### Function Pointer Parameters
#functionpointerparameters
We cannot define parameters of function type, but can have a parameter that is a pointer to a function. 
As with arrays, remember that when we use an array, we are actually just "using" the array's pointer to the first element. 
Using the function will just return the pointer to that function.
Therefore, we will have something that looks like this: 
```
// third paramter is a function type and is automatically treated as a pionter to funcion 
void useBigger(const string &s1, const string &s2, bool pf(const string &, const string &));
// equivalent declaration: explicitly define the parameter as a pointer to function
void useBigger(const string &s1, const string &s2, bool(*pf)(const string &, const string &)); 
```

When we pass a function as an argument, we can do so directly, it will automatically converted to a pointer. 
```
// automatically converts the function lengthCompare to a poitner to function
useBigger(s1, s2, lengthCompare);
```

We can use type aliases with `decltype` to make this easier and shorter in practice. 

```
// Func and Func2 have function type
typedef bool Func(const string&, const string&);
typedef decltype(lengthCompare) Func2; // equivalent type

// FuncP and FuncP2 have pointer to function Type
typedef booll(*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *FuncP2; // equivalent type
```
Both `Func` and `Func2` are function types. 
`FuncP` and `FuncP2` are pointer types. 
`decltype` will return the function type; automatic conversion to pointer is not done. 
Therefore we have to add the `*` ourselves here. 
We can then write
```
void useBigger(const string&, const string&, Func); // function type
void useBigger(const string&, const string&, FuncP); // function pointer Type
```

Declaring the same function, the first case, the compiler will automatically convert the function type represented by `Func` to a pointer. 

Here is a good way of phrasing and looking at it, using the same idea. 
Without a function pointer in a parameter/argument: 
```
// declaring functions first
int add(int a, int b); 
int subtract(int a, int b); 

typedef int(*ArithmeticFunction)(int, int);
typedef void(*StringFunction)(string);

// function defintions
int add(int a, int b){ 
	return a + b;
}
int subtract(int a, int b){ 
	return a - b; 
}
void print(string a){ // this is a decoy one that won't work later on
	cout << a << "   :" << endl;
}

int main(){ 
	ArithmeticFunction functPtrAdd = &add;
	ArithmeticFunction functPtrSub = &subtract; // notice no () on either
	ArithmeticFunction functPtrPrint = &print; // this will result in an error
	// However if we did this; 
	StringFunction functPtrPrint = &print;
	auto functPtrAdd2 = &add;
	int (*PointerToSub)(int, int) = &subtract;
	

	int result1 = functPtrAdd(10,5);
	int result2 = functPtrSub(5,10);
	cout << result1 << "   " << result2 << endl;


}
// for some reason, the compiler felt that cout was ambiguous here (function matching), so i just put std::cout in the actual code. 
```

All of this is really simple. 
The whole `int (*PointerToSub)(int, int) = &subtract` is the crux of the whole thing. 
```
int performOperation(int a, int b, int (*operation)(int, int));

// declaring a function that returns an in
// paramters are two ints
// and a pointer to a function, locally called operation, that returns an int, and takes two ints as arguments
// basically the idea is that we are passing a function into this function, through the arguments

// defining this function

int performOperation(int a,  int b, int (*operation)(int, int)){ 
	return operation(a, b);
}

// then we can add to int main(){ }

int result3 = performOperation(10, 5, add); // or could use &add in the argument
// the main point being this

int result4 = performOperation(10, 5, functPtrSub);
```

Super obvious now, that you can just put a pointer to a function in there, the idea is super simple. 

#### Returning a Pointer to a Function
#returningapointertoafunction

As with arrays, we cannot return a function type, but can return a pointer to a function type. 

By far the easiest way is to again use type alias, and `typedef`. 

```
using F = int(int*, int); // F here is a function type, not a pointer
using PF = int(*)(int*, int); // PF is a pointer type
```

The thing to remember is that the return type is not automatically converted to a pointer type. 
We have to explicitly specify that the return type is a pointer type: 
```
PF f1(int); // PF is a pointer to a function ; f1 returns a pointer to a function;

F f1(int); // ERROR F is a function type, f1 can't return a function

F *f1(int); // ok as explicitly specifying that the return type is a pointer to a function
```

Of course, we can also declare `f1` directly, which we'd do so with: 
`int (*f1(int))(int*, int);` what even is this. 

Read from inside out, we see that `f1` has a parameter list (int), so f1 is a function. `f1` preceded by a pointer, so we know that it returns a pointer. The type of that pointer itself has a parameter list, so the pointer points to a function, which returns an int. 

The `int` next to f1, means that that function has a parameter of an int, and returns a pointer to a function that takes two integer parameters and returns an integer. 

Remember the idea of trailing declarations: 
`auto f1(int) -> int (*)(int*,int);`

This one is the clearest form for me: in that the f1 function returns a pointer to a function that returns int, and has parameters of int* and int. 

I think the main thing here is this notation for a pointer to a function: `int(*)(int*, int)`

```
using PF = int(*)(int, int); // PF is a pointer to a function that returns int and takes int,int

typedef int (*PF)(int, int);
```


```
// Function that returns a pointer to a function
int (*getArithmeticFunction(bool useAddition))(int, int){ 
	if (useAddition){ 
		return &add}}
		etc. etc. 

int add(int a, int b){ 
	return a + b;
}
```

So the function `getArithmeticFunction` takes a boolean parameter, and returns a pointer to a function that returns an int, and takes two ints as arguments. 
You might think of it as this, the actual function that we're writing the body for next, in brackets, that has a pointer operator before it (in the brackets). 
Then you write the return type of the function that we are pointing to. 
This would be without using `using or typedef`

```
int functionA(); 
int functionB();

int (*getFunctionPointer(bool useA)){ 
	if (useA){ 
		return &functionA;
	}
	else { 
		return &functionB;
	}
}

int functionA(){ 
	return 10;
}
int functionB(){ 
	return 20;
}

int main(){ 
	bool useFunctionA = true;
	int (*funcPtr)() = getFunctionPointer(useFunctionA); // (*funcPtr) dereferences the pointer


}

```

See if like this `int (*rAdd)(int, int);`: `rAdd` just "disappears" and converts of a function, therefore the whole thing is rAdd is a pointer to a function that returns int and takes two ints as arguments. 

As we would with other points, we have to specify the type to which it points to, we have to specify about the function that we are pointing to. 


This is my own version that I cooked without using `using` and `typedef`:
```
void funcPrint(string);
void (*getPrintAddres())(string); // This function doesn't take any arguments, does return a pointer to a function that doesn't return anything and takes a string as a parameter
// the thing that is trhowing you off is the idea that the return type is spread around the other function
// you can also write it like this: 

auto getPrintAddress() -> void (*)(string)

void funcPrint(string a){ 
	cout << a << endl;
}

void (*getPrintAddress())(string){ 
	return &funcPrint;
}

int main(){ 
	void (*printAddress)(string) = getPrintAddress(); 
	printAddress("Hello");
}

OUTPUT: 
"Hello"
```
Note that we use `getPrintAddress()`, without having to dereference, but we can: 
`(*printAddress)("Hello World");`
#### Using `auto` or `decltype` for Function Pointer Types
If we know which function(s) we want to return, we can use `decltype`, to simplify writing a function pointer return type. 


Just remember that `decltype`, will return type `array` and type `function`. 

We could do something like this: that is written in the Associative Containers in order to get a pointer to a function: 
```
bool compareString(const string& s1, const string& s2)
{ 
	return s1.size() < s2.size();
}

int main(){ 
	decltype(compareString)* f = compareString;

	// this just gives f a function pointer 
	// bool (*f)(const string& s1, const string& s2);
}
```

