### Paradigms
C# is a primarily an object orientated language. 
Starting, realistically at the high level abstractions. 
C++ is a multi-paradigm language, supporting procedural, OOP and generic programming. 

C# is meant to be safer than C++. 
Mostly for developer productivity and easing pains with the .NET environment. 


### Memory Management
C# has automatic memory management, via garbage collection. 
The CLR will be tasked to this. 
No direct use of pointers in safe code. 

As you know in C++ we have  smart pointers, aren't they great. 



### Type Safety
In C++ there are implicit conversions, with some static and dynamic typing eg. `void*` at run time. 
C# is a strongly type-safe, with built-in type checking at compile-time and run time. 
Has many checking and error safe features like bounds checking on arrays etc. etc. 

### Platform Dependency
C++ is platform agnostic, code to be compiled into the native language that we want, there may need to be some specific code required for each platform. 
C# has .NET Core allowing for easy support on most platforms. 


### Compilation and Execution
Compiled directly into native machine code, there is no intermediate **language**, there is object code, however, that's native etc. 
C# is translated into that intermediate language, the CIL, MSIL, then JIT compiled to native code. 
The JIT makes things just a bit slower, but really not that noticeable etc. 

### Standard Libraries
C++ has our favourite STL (out lovely `std`), providing rich sets of algorithms. 
C# .NET provides an extensive and cohesive Base Class Library (BCL), which covers most things from file I/O to we services, UI frameworks and more. 
There are very few dependencies in C# therefore, don't really have to do this whole thing of including and libraries lookup etc. etc. 

### Concurrency and Parallelism
Much more mainstream in C#, simple features such as `async` and `await`. 
The C# Task Parallel Library (TPL) and PLINQ  providing high level abstractions for parallelism and concurrent execution, making it much easier than in C++. 

Remember the C++ `<thread>` library. 


