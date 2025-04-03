![[Pasted image 20240421205253.png]]There are certain conventions: 
- **declare** in header, and... 
- **define** in the corresponding source file

For a function that **inline, constexpr, or consteval** then 
- **define** it in the header file, and... 
- that's it
These are just the better practices, they are not strict strict rules: 
![[Pasted image 20240421205526.png]]
These are the few basic rules: 


Why not define all functions in headers: because the dependencies, will have to be recompiled. 
A simple declaration in the header file, it's like a heads up. 

The best way to think of the "Special Cases" is to know how compiling and linking work, not relying on a off-case scenario that might answer the question or might not, leading to ambiguity. 

![[Pasted image 20240421210003.png]]
So that understanding is crucial. 

A **declaration** is a program statement that says to the compiler: 
- "Here's a name and some attributes for an entity"
- "It might be here, or it might be somewhere else". 

A change to a header file is much more troublesome. 

A **definition** is a more complete declaration that says: 
- "Here's a name and the complete set of attributes for an entity that's right here"

All **definitions are declarations**. 
**Not all declarations are definitions**. 
![[Pasted image 20240421212252.png]]


A function declaration: 
```
int foo(int n); // non-defining declaration
```

Tells the compiler everything it needs to calls this function. 
So here we generate code that `calls` this function.

It doesn't tell the compiler how to generate the code for the **function itself**. 

