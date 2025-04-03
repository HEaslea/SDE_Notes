The name of the function must be declared before we can use it. 
Like other things, it can be defined only once, but declared many times. 

We may declare a function, that is never defined, so long as we never use it. 

The declaration has no function body, a semicolon replaces there function body. 

There is no need for the parameters. They can be useful, however, to help the users know what the function does. 

```
void print(vector<int>::const_iterator beg, vector<int>::const_iterator end);
```
No function block. 
Aka function prototype. 

#### Function Declarations go in Header Files
Variables are declared in header files and defined in source files. 
For the same reason, functions should be declared in header files and defined in source files. 

It's legal to put a function declaration directly in each source file that uses the function. However, doing so is tedious and error-prone. 
When we use a header file for our function declarations, we can ensure that all the declarations for a given function agree. 
The reason being is that if we define the function in every header file, every source file that it is included in would also "define" that function, and the compiler would be like "like man what are you doing man, there's too many of them man". 
Therefore we just have to declare them in there. 

The source file that defines a function should include the header that contains that function's declaration, verifying that the definition and declaration are consistent. 
Simple as: the header that declares should be in the source that defines. 

[[Separate Compilation]]