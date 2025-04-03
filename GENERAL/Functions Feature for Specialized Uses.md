## Default Arguments
Parameters that have particular values in most, but not all calls. 
Default arguments can be called with or without those values. 

Eg. we might use a string to represent the contents of a window. 
By default, we might want the window to have a particular height, width and background character. 
But also allow users to pass their own values in. 

To accommodate both we declare as followed: 
```
typedef string::size_type sz;
string screen(sz ht = 24, sz wid = 80, char backgrnd = '');
```
There is a default for every parameter here. 

Exactly like in python, iirc. 
HOWEVER, if one parameter has a default argument, then the all the following ones must do as well. 

My example: 
```
void printout(int i, string s = "Hello World", const char c = 'a'){
    cout << s << "   " << i << "   " << c << endl;
}

int main(){
    printout(30);
```

You would have to call printout with at least the `int` here, otherwise there would be an error. 
The output here would be: 
`Hello World!   30    a`
Really very simple. 

However, some of the issue is that if we wanted to give the `const char c` a different char, we would also have to supply the int and the string. 

We cannot omit trailing arguments. 

Remember that variadic arguments take a variable number of arguments. 

Trailing arguments have this idea behind them: 
`void myFunction(int fixedArg, ...);`
`myFunction(10, "hello", 3.14);`
Here, `"hello"` and `3.14` are trailing arguments. 
You can also use `initializer_list`'s as well, which looks very similar too: 
Creating a template for the same effect:
```
template<typename T, typename... Args> 
void myFunction(T fixedArg, Args... args);
```


Initializer Lists: 
```
#include <initializer_list>
#include <iostream>

void myFunction(std::intializer_list<int> args){ 
	for(auto arg : args){ 
		std::cout << arg << "  ";
		}
	std::cout << std::endl;
}

int main(){ 
	myFunction({1,2,3,4,5,6}); // Output: 1 2 3 4 5 6
}
```

Remember that initializer lists are only limited to a single type. 
And you cannot pass arguments of different types as you can with variadic templates, a bit limited in that way. I think the book talks about variadic types later on. 

Anyway, back to the trailing arguments issue, we wouldn't be able to do something like this: 
`window = screen(,,'?'); // can omit trailing arguments`
`window = screen('?'); // would give us something like ('?',80,'')`

This works as the char can be converted to a string. Weirdly as well, the char can be converted to a `string::size_type` and is passed as the argument to height, weirdly, the ? is converted to `0x3F`, which is decimal 63. That would be passed over to the function. 

The trick here, is that when designing a function with default arguments, we would sort them in a way that works the best for users. 

### Default Argument Declarations
Although it is normal practice to declare function once inside a header, it is legal to redeclare a function multiple times. 
Each parameter can have its default specified only once in a given scope. 
Any subsequent declaration can add a default only for a parameter that hast not previously had a default specified. 

No default for the height or width parameters: 
`string screen(sz, sz, char = '');`

Again remember that defaults can only be specified only if all the parameters to the right already have defaults. 

We cannot change an already declared default value: 
`string screen(sz, sz, chaqr = '*');`



Local variables are not allowed as default arguments. 
A default argument can be any expression that has a type that is convertible to the type of the parameter. 

```
sz wd = 80;
char def = '';

sz ht(); 
string screen(sz = ht(), sz = wd, char = def);
string window = screen(); // calls screen(ht(), 80, '')
```

The value that those names represent is evaluated at the time of the call: 
```
void f2(){ 
	def = '*'; // changes the value of a default argument
	sz wd = 100; // hides the outer definition of wd but DOES NOT CHANGE THE DEFAULT
	window = screen(); // calls screen(ht(), 80, '*');
}
```

We changed the value of def. The call to screen passes this updated value. 
Our function also declared a local variable that hides the outer `wd`. 
However, the local named `wd` is unrelated to the default argument passed to `screen`.

