C++ template metaprogramming that gives you the ability to inspect and transform the properties of types. 
Given `T`, we could ask the compiler: is it an integer? It is a function? Is it a pointer? Does it have a destructor? Can I copy it? 
#conditionalcompilation
Useful in `conditional compilation` , where we instruct the compiler to pick the right path according to the type in input. 


Also given `T`, you can add/remove the `const` specifier, the reference or the pointer, or yet turn it into a signed/unsigned type and many other crazy operations. 
The beauty here is that everything takes place at compile time, with no run time penalties; it's template metaprogramming after all. 

