## Stringizing Operator
`#` which will convert any macro parameters into string literals, without expanding the parameter definition. 

Used only with macros that take arguments. 

Some Examples: 
```
// stringizer.cpp

#include <stdio.h>
#define string( x ) printf_s(#x "\n");

int main() 
{ 
	// this looks so weird, however, is actually fine
	stringer( In quotes in the printf funciton call);

	// as in this prints "in quotes when printed to the screen", with the quotes
	stringer( "in quotes when printed to the screen");

	
	stringer ("This \" prints an escaped double quote");
}
```

```
#include <stdio.h>

#define stringer(x) printf_s(#x "\n");

int main() {

    stringer(hello there)

    stringer("Printing double quotes maybe?")

    stringer ("This \" prints an escaped double quote")

    return 0;

}
```

![[Pasted image 20240907012845.png]]

So the `""` are sort of used in a different way here, which is kind of crazy. 

They are expanded in this manner: 
```
int main() 
{ 
	printf_s("hello there");
	printf_s("\" Printing double quotes maybe?");
	pritnf_s("\"This:\\\" prints an escaped double quote\"" "\n");
}
```

Remember that `\\` is used to dereference, so `\\\` is used to write `\` in a string etc.  

```
hello there
"Print double quotes..."
"This : \ prints..."
```


```
#define F abc
#define B def
#define FB(arg) #arg
#define FB1(arg) FB(arg)
#define outer(arg) printf_s(FB1(arg));

int main() {
    outer(F)
    outer(B)
    outer(F B)
}
```

![[Pasted image 20240907014250.png]]
Which looks really odd. 

## Charizing Operator (#@)

```
#define makechar(x) #@x
```

The actual argument is enclosed in single quotation marks and treated as a character when the macro is expanded. 

Causing a pattern such as: 
```
a = makechar(b);
a = 'b';
```
Which makes a lot of sense.


## Token Pasting Operator

Sometimes known as the combining or merging operator: 
Permitting separate tokens to be joined into a single token, and therefore, can't be the first or last token in the macro definition. 

Like + for token essentially. 

These can then be stringized, however, for now they are just tokenized, remember that before these are variables, they are just bits of text, and we can do what we want with them. 

```
#define paster(n) printf_s("token" #n " = %d", token##n)

int token9 = 9;

paster(9);
```

The macro here will yield: 
```
printf_s("token" "9" "= %d", token9);
```

Then the strings concatenate: 
```
printf_s("token9 = %d", token9);
```



#### THERE IS AN ERROR WITH THIS CODE
```
#define printer(arg) printf_s("Token " #arg " %d ", token##arg);

int main() {
    int token1 = 1;
    int token2 = 2;
    int token3 = 3;

    for(int i = 1 ; i <=3 ; ++i)
    {
        printer(i);
    }
}
```

There is an issue with this code: As `i` isn't evaluated in the macro at all. 
It's just evaluated to `i`


[This Fluent C++ Page About It](https://www.fluentcpp.com/2021/03/12/cpp-fold-expressions/)

An instruction for the compiler to repeat the application of an operator over a variadic template pack.

A simple sum example: 
```
template <typename ... Values> 
auto sum(Values const& ... values)
{ 
	// code here
}
```

The implementation would look something like this : 
```
template <typename ... Values> 
auto sum(Values const& ... values)
{ 
	return(values + ...);
}
```
This is right associative: 
`return value1 + (value2 + value3)`

Left associative would be: 
`return (value1 + value2) + value3`
Which we would get to with `return (... + values);`

Leading to **ASSOCIATIVITY IS ON THE SAME SIDE AS THE DOT DOT DOT**. 

Associativity matters with: 
```
template<typename ... Values> 
auto f(Values const& ... values)
{ 
	return (values - ...);
}
```

Which will lead to `value1 - (value2 - value3)`

Empty Packs can cause errors, so you have to find a way to get around that. 

So with the whole thing about
`std::cout << (... << args) << std::endl;`, which is left fold association. 
Meaning that we get something along the lines of: 
`((std::cout << value1) << value2)<< value3`

However, if we did something like this: 
using right fold
`std::cout << value1 << (value2 << value3)`
Not quite right I don't think, however, you get the point, so first we do a << operator on value 2 and value3, if that is defined, or even not, then we don't get the result of outputting with `std::cout`. This is the error here, however, left fold association will work just fine. 
That is because we are using `std::cout` just fine. 

Where this doesn't really matter is something like this: 
```
tempalte<typename ... Args> 
bool all(Args ... args)
{ 
	return(... && args);
	// can also just be return (args && ...)
}
```


### Binary Left Fold Expression
`(init op ... op pack)`
Looking like this: `0 - ... - args`. 
Which is still left side associative: 
`(((0 - value1)- value2)-value3)`
### Binary Right Fold Expression
Just exactly the same but right associative. 

```
void printOut(const auto& p)
{
    std::cout << p << std::endl;
}

template<typename ... TArgs>
void printer(TArgs ... args)
{
    (printOut(args), ...);
}

int main() {
    printer(1, 2, 3, 4, 5);
```

Using the comma operator here, preserves the left-to-right evaluation order. 

This is a binary left fold over the comma operator and it expands like this: 
`(printOut(arg1), printOut(arg2), ..., printOut(argN));`

[Joe Chu's Blog About It](https://chuzcjoe.github.io/2024/05/19/cpp-fold-expressions/)

He goes into way more detail about it. 
```
template<typename T, typename... Ts>  
auto average(T value, Ts... values) {  
    auto n = (1. + sizeof...(Ts));  
    return ((value / n) + ... + (values / n)); // (init op ... op pack)  
}  
  
int main() {  
    std::cout << average(1.0, 2.0, 3.0, 4.0, 5.0);  
    return 0;  
}
```


![[Pasted image 20240909213304.png]]
`sizeof...` is it's own operator, which queries the number of elements in a parameter pack. 

## The Comma Operator 
Allowing to fold and unfold to perform multiple operations in a sequence. 
eg. 
```
template<typename Container, typename... Values>  
void push(Container& c, Values... values) {  
    (c.push_back(std::forward<Values>(values)), ...);  
}  
  
int main() {  
    std::vector<int> vec{5};  
    push(vec, 1, 2, 3);  
    for (int v : vec) {  
        std::cout << v << "\n";  
    }  
}
```

This one expands, 
```
c.push_back(std::foward<Values>(v1)), 
c.push_back(std::foward<Values>(v2)),
etc. etc. 
```

