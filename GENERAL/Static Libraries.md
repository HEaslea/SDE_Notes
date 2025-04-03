In order to better partition our C++ Projects, we need to look at static libraries. 
They are precompiled code that we can link into our main project: 

### The Linker
The linker is responsible for linking symbol references, finding the correct implementations for functions, classes and vars. 

The Compiling Stage: 
- We compile src files, generating object files for each, they will have unresolved references to any functions or symbols declared but not defined within that file. 

The Linking Stage: 
- Combining all the object files and libs to create the final executable. 
- Resolving unresolved symbols in your code by searching through the object files and static libs your provided. 

When we use  `g++` commands, then we have `.o` files being created, that will possibly have unresolved references. 

Then the linker will resolve all these into one executables. 

#### How to Link
The linker will search for symbols in the libs and object files in the order they are provided in the command line (big thing). 

If not found by the end of the file, it will just move on. 
`g++ -o main main.o -L. -lmylib`

The linker will first check `main.o` and then `libmylib.a`. 

```
// main.cpp 
#include "mylib.h" // notice that we need this here

int main() 
{ 
	foo(); // the linker must find this symbol
	return 0; 
}
```

```
// mylib.h
void foo();
```

```
// mylib.cpp
#include "mylib.h"
#include <iostream>

void foo()
{ 
	std::cout << "Hello From Foo" << std::endl;
}
```

Then compile the library: 
`g++ -c mylib.cpp -o mylib.o`
Then archive the lib
`ar rcs libmylib.a mylib.o`


Compile the program: 
`g++ -c main.cpp -o main.o`
Link the program
`g++ -o main main.o -L. -lmylib` // the linking

`./main`

Then that's it. 

We still need the header files that the static library uses. This means that we can use the interface that will be found in the static library. 
The idea is that we have the machine code whenever 


## What? 
The whole idea is that we have a precompiled file that we can link against. 
There is the idea of reusability: we don't have to recompile those files all the time. 
Encapsulation: The user of the library cannot see the source code and doesn't need the implementation detail - just the interface that we get from the header. 
Efficiency: saving compile time. 


##### Dynamic Libs
`.dll` or `.so` on Linux: 
This is linked at run time. 

The executable we have knows where to the find this type of lib, but the actual code is loaded from the lib during execution. 
These are known as shared libs, as they can be shared between files. 

This looks so different in Linux, but on windows: 
`g++ -shared -o mylib.dll mylib.cpp` 
This will compile into a `.dll` file. 

Linking these types of files to the Executable: 
`g++ -o main main.cpp -L. -lmylib` telling the compiler to link against `libmylib.so`
`-L.` where the lib will be found

Here we will still need the header files in our own code, for the interface, the declarations, classes etc. that the dll will provide. 

[[The CMake Language]]