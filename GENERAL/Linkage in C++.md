## External and Internal
**External** : - The symbol is visible and accessible across different translation units. This is the default for non-static globals and non-static functions. 

**Internal** : - Restricted to the translation unit in which it is defined. Achieved, mostly with the `static` keyword. 
Internal linkage allows for safe redefinition of symbols within different translation units without causing redefinition errors. 

**No Linkage**: Symbols like local variables within functions have no linkage and are accessible only within their scope. 

Remember that when we write an implementation file (`.cpp`, `.cxx`), the compiler will generate a **translation unit**. This is the source file that we wrote plus all the headers you `#include`d in it. 

`extern` - obviously external linkage in the translation unit. 
`static` - internal linkage. 

```
int i; // extern by default 
const int ci; // static by default
extern const int eci; // explicitly extern
static int si; // explicitly static

// similary for function
int f(); // extern by default
static int f(); // explicitly static
```

Note that instead of using `static` (for internal linkage), it's just better to use `anonymous namespaces` into which you can also put classes: 
```
namespace 
{ 
	int i; // extern by default, but unreachable from other translation units
	class C; // extern by default again, however, unreachable from other translation units
}
```

#### Header Files
Without header files, you'd have to manually copy declarations across every `.cpp` file that need them, which is error-prone, hard to maintain etc. 

We have to declare symbols before they are used, so that the translation unit knows what we are going to use before we use it. 

Note that when something has external linkage, it means that it can be used in multiple `.cpp` files. 
HOWEVER, each `.cpp` file must know about a symbol in order to use it. 

Here **Declaration**: specifies the name and type, and linkage of a symbol without allocating memory or providing an implementation: 
`extern int globalCounter`. 
However, **Definition**: `int globalCounter = 0;` provides some implementation for the symbol. 

When we reference a symbol in `.cpp` file, the compiler will have to know: 
- The Type of the symbol: so it knows how to use it properly (what parameters a function takes or what type a variable is). 
- The symbol's name : to ensure it can resolve the reference to the right symbol during compilation. 

Say that we have `utils.cpp` `utils.h` and `main.cpp`: 
```
// utils.h
#ifndef UTILS_H
#define UTILS_H

int add(int a, int b);

#endif
```

```
// utils.cpp
#include "utils.h"

int add(int a, int b)
{ 
	return a + b;
}
```

```
// main.cpp

#include "utils.h" 
// think here about all the declarations that just get added here
int main()
{ 
	int result = add(5, 3); // use the fucntion declared in utils.h
}
```
Even through `add` is defined in `utils.cpp`, `main.cpp` will need to use the declaration that we have in `utils.h` in order to actually use the function. 

During the compilation phase of `main.cpp`, the compiler needs to know: 
- The signature of `add` (ie. it takes two `int` parameters and returns an `int`). 
- The symbol name `add` to reference it correctly. 

During the linking stage: after compilation, during the linking stage (remember here that we multiple `.o` files). The linker will connect all these references to `add` in `main.cpp`, with the actual definition. This is only possible if `main.cpp` has a declaration of `add` to reference it correctly during compilation. 

If we were to define everything in every file, then we will run into **Multiple Definition Error**: 

### Extern in Header Files
When it comes to header files, remember that we simply just paste everything in the header file into the cpp file. 
`extern` - this symbol exists, but its definition is somewhere else, so don't create a new definition here. 


```
// counter.h

#ifndef COUNTER_H
#define COUNTER_H

extern int globalCounter;

#endif
```

```
// utils .cpp
#include "counter.h"

int globalCounter = 0; // definition and initialization of globalCounter
```

Although this cpp above, sees the same declaration but has the actual definition `int globalCounter = 0`, telling the compiler that `globalCounter` is defined in `utils.cpp` and should allocate memory for it. 

```
// main.cpp
int main() 
{ 
	globalCounter++; // uses the globalCounter declared in counter.h 
}
```

Then during linking stage, the linker resolves the `globalCounter` references in `main.cpp` to the actual definition in `utils.cpp`. 

Here, if we didn't use `extern`. 
```
// counter .h
int globalCounter; // without extern this is a definition
```
Think about this, this will be pasted into every `.cpp` that includes it. Therefore we will be getting a bunch of definitions of the same symbol. 

The contents of a header file will be included verbatim. 

When we use `extern` in header files, that is the same as declaring in CPP files, which is fine. 

The actual definitions should only be in a single `.cpp` file. 

### `inline` 
`inline` does something similar. 
When we have a function in a header file, and include that in multiple CPP files, it can lead to multiple definition errors at link time because each cpp file ends up with a separate definition of hte same function. 
The `inline` means that, "Woah that's cool don't worry" as long as they are identical!
This is because they have internal linkage by default. 
If you notice that this is similar to `static`. 

```
// math_utils.h
// ifdefs writetn

inline int add(int a, int b)  // inline
{
	return a + b;
}
```

Then we include `math_utils.h` in multiple `.cpp` files.
```
#include "math_utils.h"

int main()
{ 
	auto something = add(3, 4);
}
```

Although if we define the function in the header, and that is included to others, then we run into the error that they are defined in every `.cpp` files. 

Therefore: `extern` is used to denote that we are declaring wherever it is included, unless we actually do define it. 

`inline` suggests the inlining of function code (paste it directly where it's called). This does allow multiple definitions of the same function in different translation units without causing linker errors. 

`extern` will not affect whether a function is inline or not, only the visibility and ensuring that only one definition is found. 
`inline` allows the function to have external linkage by default, the compiler/linker treats multiple definitions of an `inline` function as a single entity. 

Use `extern` when we want to declare global variables or functions that are defined elsewhere. 

Use `inline` in header files when you want to define small functions directly in the header file and have them available in multiple .cpp files without causing linker issues. 

### Class Definitions
```
// MyClass.h

class MyClass 
{ 
public: 
	void doSomething(); 
private: 
	int x;
};
```
This, THIS is just a blueprint of a class, there is no allocation of memory so far, including this in multiple files, is absolutely fine, as it doesn't cause any memory allocation or implementation duplication. 
One way to think about it, is to think, what causes memory allocation, and if there is, then we need to point to it, if it has the same name. A declaration, is just to say, yeah this is a thing, but not here, somewhere it does exist in memory. 

However, templates are usually defined in header files, because they need to be visible to all translation units that instantiate them. 

This is also why we can't define a `static` member variable of a class, as when we have classes included in other files, there will be more than one definition etc. 

#### Memory Allocation and Multiple Definitions
The linker only expects there to be one definition of each symbol in the entire program. 

`int globalVariable;` this is a definition and allocates memory. 
Here, if this were in a header file, and we put this into multiple `.cpp` files, then each would try and allocate memory for `globalVariable`. 

What allocates memory: 
- Global variables that are defined without `extern` in a header file. 
- Non-`inline` functions defined in a header file and included in multiple `.cpp` files. 
- `static` member variables of a class that need to be defined in exactly one `.cpp` file. 

Class definitions do not allocate memory by themselves. 
This will just tell the compiler what the actual structure will look like, and what the interface is. 

The memory will be allocated when we do something like this: `MyClass obj;` , so here Memory for `MyClass` is allocated here. 

Everything that goes into a header file, should not be allocating memory, otherwise we run into errors where we have multiple definition errors. 

Internal linkage, will mean that we will have no redefinition errors when it comes to that symbol. 
