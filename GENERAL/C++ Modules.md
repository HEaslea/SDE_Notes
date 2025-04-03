Of course we know that C++ uses the header file model. 
In source files, we include these header files, that contain declarations. 

During the preprocessing stage: the preprocessor handles `#include` directives by copy pasting content of header files into source files. 

Each source file is compiled separately, then the linker combines them. 

Some of the issues with this: 
1. Long Compilation Times: Sometimes we have some redundant parsing. 
2. When we change something in the header files, we may trigger recompilation, even if we are not directly changing those parts.
3. Global NameSpace pollution: reading the other C++ software design book, we look at the ways that we have to avoid this global namespace pollution. 

#### Modules
A modern alternative to the header file model by introducing a modular compilation approach. 
Allowing developers to define **self-contained units** of code with explicit interfaces, improving compilation efficiency and code organization. 

The idea is that there are clear separations of interface and implementations. 
The main thing being improved and faster compilation times. 
Better dependency management. 

1. Faster Compilation Times - They are precompiled into binary form, with no repeated parsing (they are only parsed once, not like header files). 
2. Enhanced Encapsulation and Namespacing - symbols in a module are not put in the global namespace, they must be explicitly exported. There is no macro inclusion, meaning we bypass any of the issues there. 
3. Cleaner code organization - there will not have to be any more include guards - `ifndef define endif`. 
4. There will be better IDE integration, tools can better understand module boundaries and dependencies - and will be able to give better references etc. 
5. Safer and more reliable code. 

### Basic Syntax and Usage
```
// File : math_module.cppm
export module math; // define and name the module

export int add(int a, int b) // accessible by other modules
{ 
	return a + b;
}

int multiply(int a, int b) // internal use only
{  
	return a * b;
}
```

Similar to python, then you import the module: 
```
// File : main.cpp
import math;

#include <iostream>

int main() 
{ 
	int sum = add(3, 4); // add was exported, therefore we can use it 
	// int product = multiply(3, 4); // not accessible
	std::cout << "Sum: " << sum << std::endl;
}
```
This just seems really obvious. 

In order to have internal linkage in header files we have to use `static`: 
```
// my_header.h
static void internatalFunction()
{ 
	....
}
```

We use this when we want to use these functions in the file, however, it should not be leaving that file. 
This is not erroneous, as they have internal linkage, they are not linked inbetween units, therefore we have no multiple definition errors. 

The issues with this is that 
- Each translation unit gets its own copy of the `static` function, leading to multiple instances in the final binary. 
- Unnecessarily increases the size of the file. 
- Fucking awful at debugging, as there are so many of the same function. 

An alternative is the unnamed namespace: however, we still have the same drawbacks. 

This is just something that we will have to remember, before modules become the everything and anything. 

**Internal Linkage** : an identifier with internal linkage can be referred to only within the same translation unit. Using the `static` keyword at the global scope or those inside an anonymous namespace. 

**External Linkage** : an identifier that can be referred to across multiple translation units. Functions and variables that are non-`static`, at the global scope have external linkage. 

```
// globals.h
#ifndef GLOBALS_H
#define GLOBALS_H

exter int globalVariable; // Declaration with extern

#endif // GLOBALS_H
```

```
// globals.cpp
#include "globals.h"

int globalVariable = 42; // Definition
```

```
// main.cpp
#include "globals.h"

int main() 
{ 
	std::cout << globalVarible << std::endl;
}
```