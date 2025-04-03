From source code, we have the C# compiler (`csc.exe`), turning it into an intermediate language **CIL** (Common Intermediate Language), AKA MSIL (Microsoft Intermediate Language). 
**This is a CPU - independent set of instructions that can be efficiently converted to native code.**

 This intermediate code is then packed into an assembly. 
 They are typically of the form `.exe` or `.dll` files. 
 They include metadata of the types, methods, and other information in your program, which is crucial for .NET runtime. 


### JIT Compilation
**Just-in-Time** - When we run a C# program, the .NET run time takes the CIL, and performs a JUST IN TIME Compilation. 
This will turn it into **native machine code**, which is what we execute. 

The .NET runtime, upon execution will provide services like garbage collection, exception handling, and type safety during execution. 

These compilers can also look at optimizing, dependent on the specific platform that it is running on. 


### AOT Compilation
**Ahead-of-Time** - compiling before run time, into native code, using tools like **NGen**  (Native Image Generator), or through techs like **CoreRT** or **Mono's AOT**. 
This will reduce startup time, duh, potentially improving performance. 


### Cross-platform Exe
C# and .NET ecosystem are designed to be cross platform. 
The .NET runtime is responsible for managing the execution environment on each platform. Ensuring consistent behaviour for each OS. 

## CLR - 
is .NET's runtime environment. 
It will manage memory with garbage collection. 
Not like in C++. 

C++ will compile directly to native machine code. 
C# uses the intermediate language, then compiled at run time into native code with the JIT compiler. 
