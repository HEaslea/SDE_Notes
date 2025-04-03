#braceinitialization
## Brace Initialization
`int i{0};` 
Ensuring type safety and prevents narrowing conversions. 
If you, with this type, try to initialize an `int` with a `double`, then we will run into an error, as we cannot initialize with a type that is "larger" than `int` in this case. 

eg. `int i{3.14};` is an error

This does help to avoid the most vexing parse, which is a syntactic ambiguity in C++. 

Either with: 
```
Widget w{Widget{}};
Widget w2{}; 
```
Both do the same: 
However: 
`Widget w();` will be a function declaration. 


## Parenthesis Initialization
`int i(3.14);` `i = 3 in this case`.
Allows for narrow conversions. There is an implicit conversion. 
Can be ambiguous, with the most vexing parse. 
`int i();` is a function declaration. 

```
#include <iostream>

int main() {
    int a{0}; // Uniform initialization (preferred)
    int b(0); // Direct initialization

    // int c{3.14}; // Error: narrowing conversion not allowed
    int d(3.14); // Allowed: `d` will be 3

    std::cout << "a: " << a << "\nb: " << b << "\nd: " << d << std::endl;
    return 0;
}

```

Avoiding most vexing parse: 
```
#include <iostream>

class Widget {
public:
    Widget() { std::cout << "Default constructor called\n"; }
    Widget(int) { std::cout << "Parameterized constructor called\n"; }
};

int main() {
    Widget w1{};   // Default initialization
    Widget w2{5};  // Initialization with a parameter

    return 0;
}

```

Another example: 
```
#include <iostream>

class Widget {
public:
    Widget(int) { std::cout << "Widget(int) called\n"; }
};

int main() {
    Widget w1(Widget(5));  // Most vexing parse, interpreted as function declaration
    Widget w2{Widget{5}};  // Correctly interpreted as a variable definition

    return 0;
}

```