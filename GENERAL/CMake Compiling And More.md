##### The Basics of Compilation
The compilers can compile in order to create an executable for almost all architectures out there. 
Translation Units are turned into `.o` files. 
Then the linker will link those object files together in order to make an executable. 
The OS will use something called a loader, will load into virtual memory, including any dyanmic libs. 

#### Compiling
- Preprocessing
- Linguistic Analysis
- Assembly
- Optimization
- Code Emission

Preprocessing : is taking all the macros, which are essentially glorified find and replaces. It will also look at the `#define`, as well as `#ifdef` `#ifndef` `#endif`. `#include` 's will also be added to the code as well. 

Linguistic Analysis : This is tokenization, lexical, syntactical, and semantical analysis. 

Assembly: turning all this into machine code, that will be CPU specific instructions, that are found on the platform that we are currently on. Therefore, compiling for that machine on that machine is a way of compiling. 
Here, we can actually have our code turned into ASM, however, some compilers don't do this, and definitely don't do this automatically. 

Optimization : Inlining etc. 

Code Emission : Get rid of unneeded code, say if a function doesn't do anything at all, no state change etc. then it can just be emitted. This marks the step of of the transformation of the ASCII code that we wrote our code in, into a binary executable file, that can be processed by a CPU. 


##### Initial Configuration
CMake offers functions for every stage of this compilation: 
- `target_compile_features()`
- `target_sources()`
- `target_include_directories()`
- `target_compile_definitions()`
- `target_compile_options()`
- `target_precompile_headers()`

`target_...(<target name> <INTERFACE|PUBLIC|PRIVATE> <arguments>)`

The thing with CMake is that when something is read, we want to be more explicit with what we are saying, if we do something like: 
`file(GLOB helloworld_SRC "*.h" "*.cpp")`

`file(GLOB <variable> [RELATIVE <path>] [CONFIGURE_DEPENDS] <glob_expression>...)`

Another: 
`file(GLOB SOURCES /absolute/path/to/files/*.cpp0`

```
add_executable(main main.cpp)
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
	target_sources(main PRIVATE gui_linux.cpp)
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
	target_sources(main PRIVATE gui_windows.cpp)
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
	target_sources(main PRIVATE gui_macos.cpp)
else() 
	// some error message
endif()
```
If you find that you are including a ton of files into one thing, then you are probably not using the structures correctly. 

### Include Files and Preprocessing
The simple idea being that `#include` will just replace all the code at that point with the code in the file, simple as. 
If that is just interface etc., then the linker is tasked with resolving all that. 
There is both `""` and `<>` files, the `<>` files are standard include directories, where they are stored in the system and not something that we have created, they will be searched for elsewhere. 
They just differentiate the difference between where we will be searching for those files. 

```
target_include_directories( <target> [SYSTEM] [AFTER | BEFORE]
	<INTERFACE|PUBLIC|PRIVATE> [item1...]
	[<INTERFACE|PUBLIC|PRIVATE> [item2...]]
)
```

This will add to the `-I`, the paths that we need to search for these files that contain the code that we need. 
The `SYSTEM`, signifying to the compiler that the given directories should be treated as standard system directories. 

##### Preprocessor Definitions: 
```
#if defined(ABC)
	std::cout << "ABC is defined" << std::endl;
#endif
#if defined(DEF > 2 * 4 - 3)
	std::cout<< //////
#endif
```

Passing in those values using CMake: 
```
set(VAR 8)
add_executable(defined definitions.cpp)
target_compile_definitions(defined PRIVATE ABC "DEF=&{VAR}")
```
`PRIVATE` meaning that other targets that depend on this one will not be passed to that depending target. 

Traditionally these are defined with `-D`. 
`target_compile_definitions(hello PRIVTE -D FOO)`
or 
`target_compile_definitions(hello PRIVATE -DFOO)`


###### A neat trick that you probably shouldn't use
```
class X 
{ 
#ifdef UNIT_TEST
	public:
#endif
	int x_;
};
```
Meaning that if we are unit testing this class, all the privates will be public: 
however, not great unit testing and class if we have to make public something that is private. See C++ design from oreilly where they say that using helper functions would be the way forward here. 


#### Configuring the Header Files
Remember that passing everything in using `-D` is quite tedious, and can be screwed over by the sheer number of defines. 
```
// config.h.in
#ifndef CONFIG_H
#define CONFIG_H

#define PROJECT_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#define PROJECT_VERSION_MINOR @PROJECT_VERSION_MINOR@
#define DEBUG_MODE @DEBUG_MODE@

#endif
```

The idea is that header files have placeholders referencing the variables, and we allow CMake to fill them in. 

Another way: 
```
#cmakedefine FOO_ENABLE
#cmakedefine FOO_STRING1 "@FOO_STRING1@"
#cmakedefine FOO_STRING2 "${FOO_STRING2}"
#cmakedefine FOO_UNDEFINED "@FOO_UNDEFINED@"
```

Then we utilize this like so: 
```
add_executable(configure configure.cpp)
set(FOO_ENABLE ON)
set(FOO_STRING1 "abc")
set(FOO_STRING2 "def")
```

Then `configure_file` will make a new file. 
```
configure_file(configure.h.in configured/configure.h)
target_include_directories(configure PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
```
The configured file will look something like this: 
```
#define FOO_ENABLE
#define FOO_STRING1 "abc"
#define FOO_STRING2 "def"
// foo undefined will be left undefined
```
`#cmakedefine` becomes `#define`. 

Then in a `configure.cpp`
```
#include <iostream> 
#include "configured/configure.h"
```

```
configure_file( <input> <output> 
	[NO_SOURCE_PERMISSION | USE_SOURCE_PERMISSIONS | FILE_PERMISSION <permissions> ...]
	[COPYONLY] [ESCAPE_QUOTES] [@ONLY]
	[NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF]]
)
```


### Config Of the Optimizer
When we write code, we write clean for readability. 
That's not going to be the optimal way to write that code. 
However, we can't write the absolute refined mess that is the optimized version. 
Therefore, we let the compiler do it for us. 
We define this by flags. 

`-O0-3`

`-O0` means no optimization
`-O3` means full optimization

```
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
	set(CMAKE_CXX_FLAGS_RELEASE "-O2 -DNDEBUG")
	set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g")
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
	set(CMAKE_CXX_FLAGS_RELEASE "/O2 /DNDEBUG")
	set(CMAKE_CXX_FLAGS_DEBUG "/O0 /DEBUG")
endif()
```


These `flags` will apply to all targets, as they are global. 

### Function Inlining
The inlining will occur when we write a function in class declaration and when we announce `inline` meaning that compiler might do it, it might not, it's a suggestion: 
```
struct X { 
	void im_inlined() { /* .... */ }
	void me_too()
};

inline void X::me_too() { /* .... */ }
```

It happens more at machine code and assembly code, therefore just putting the code at the point of calling, is not exactly correct, however mostly right. 
We bypass the creation and teardown of a new call frame and the need to look up the address of the next instruction to execute (and return to) and enhances instruction caching as they are close by. 

Remember, that the idea of copying the code will mean that that function might make all the areas that they are added so much larger. That's not that deep at the level of a PC, however, in embedded systems, it might be critical. 

Debugs might just be non-inlined, so that we don't run into these issues. 

`-O0` or by
`-finline-functions-called-once` : This is only for GCC. 
`-fineline-functions` : For Clang and GCC. 
`-finline-hint-functions` : Only for Clang. 

#### Loop Unrolling
Or loop unwinding. 
Take loops and instead just turn them into a series of statements, rather than going through the if condition, that could result in pipeline flush. (real bad). 

```
void func() 
{ 
	for(int i = 0; i < 3; i++)
		std::cout << "hello\n";
}
```

```
void func() 
{ 
	std::cout << "hello\n";
	std::cout << "hello\n";
	std::cout << "hello\n";
}
```


This really only works if the compiler can accurately assess the number of iterations. 
`-floop-unroll` for GCC
`-funroll-loops` for Clang. 
Implicit in the `-O3`. 



#### Loop Vectorization
The idea of SIMD, where we perform the same instruction on multiple parts of data at the same time. 
```
int a[128];
int b[128];

for(int i =0; i < 128; i++)
{ 
	a[i] = b[i] + 5;
}
```

As there is no dependency between the elements of each, we don't have to wait for that to finish executing before we move onto the next iteration, therefore we can do something like this: 
This sort of optimization will happen at the assembly/machine code level. 
```
for(int i = 0; i < 32; i +=4)
{ 
	a[i] = b[i] + 5;
	a[i + 1] = b[i + 1] + 5;
	a[i + 2] = b[i + 2] + 5;
	a[i + 3] = b[i + 3] + 5;
}
```

`-ftree-vectorize` `-ftree-slp-vectorize` enabling in GCC.
`-fno-vectorize` `-fno-slp-vectorize` disabling vectorization in Clang. 
The thing with this is that we shouldn't write code like this ourselves, as the optimization from the compiler **actually creates and uses an instruction that wouldn't occur without the optimization itself**. Therefore, we actually just want to write it normally and let the compiler do the rest. 

## Managing the Process of Compilation
##### Reducing Compilation Time
The idea that we are producing translation units, means that compile times are already modularized. C++ will recompile on sources that have been changed, rather than everything every time. 

#### Precompiling Header
The precompilation of headers `pch`. 
Remember the preprocessor section will just include all the code that is in the header files. 
This means that they must be recompiled every time that the `.cpp` file changes.
If multiple files use the same header files, this means a lot of recompilations. 

```
target_precompile_headers( <target> 
	<INTERFACE|PUBLIC|PRIVATE> [header1...]
	[<INTERFACE|PUBLIC|PRIVATE> [header2...]]
)
```

This is stored in the target's `PRECOMPILE_HEADERS`. 
This means that we don't need a file at all for precompiled headers, we just add to that target those header files. 
The idea is that we just include the precompiled version in subsequent compilations. 

```
add_executable(${PROJECT_NAME} main.cpp another_file.cpp)

target_precompile_headers(${PROJECT_NAME} PRIVATE pch.h) # where we define our own

target_sources(${PROJECT_NAME} PRIVATE pch.cpp)
```

`target_precompile_headers(${PROJECT_NAME} PRIVATE <iostream> <iomanip>)`
```
set(PCH_HEADERS
	<iostream>
	<iomanip>
	<vector>
	<string>
)
```
This means that we need include all these files, however, during the compilation, we use the precompiled header files, rather than parsing them every single time. 

We can even use: 
`target_precompile_headers(<target> REUSE_FROM <other_target>)`
The consuming target here cannot specify its own precompiled headers. 
ALSO, ALL THE COMPILE FLAGS, AND COMPILE DEFINITIONS MUST BE THE SAME BETWEEN THESE TARGETS. 

#### Unity Build
"unified builds" or "jumbo builds". 

`#include "source1.cpp"`

Adding code like this: means that optimizer will be working on all the code in the same file. 
This means that we are not compiling each `.cpp` file individually. 

The drawbacks are that we are reducing the amount of parallelizable work. 
Anonymous namespaces will be a no go, as they are now in the same CPP file, this is not a big thing tbh. 
This is the same for all internal linkage and external linkage, they are a little skewed here. `static `globals will be  in the main file now. 

The win is that when we want to compile all the files, however, this means, that, we do have to recompile all the files. 
The way that we do this is by saying to CMake `CMAKE_UNITY_BUILD` = `ON`. 
This will initialize the `UNITY_BUILD` property on every target defined thereafter. 
Or you could set that target property for every single thing going on in there. 

These builds can be selective in that we have a static lib and dll be unity and then the main app just be normal. 

We can also call `set_target_properties(<target1> <target2> PROPERTIES UNITY_BUILD true)`. 

The book also says that we should not do this for end users, but rather let them decide whether this is what they want. 

### Finding Mistakes
`-Werror` will treat all warnings as errors, however, this will prevent compiling until all the warnings are dealt with. Which is not really what we want. 

One main area that we would want to turn on all errors in this case, is when we want to produce a public library, where our users might be using the library in stricter scenarios than ours. 
Better,  more adjust settings might look like `-Wall` or `-Wextra`, others just might be too much. 
The main thing to think about is reading the manual for the compiler that we are using. 

###### Debugging Individual Compilation Stages
`-save-temps` for both GCC and Clang compilers. 
```
add_executable(debug hello.cpp)
target_compile_options(debug PRIVATE -save-temp=obj)
```

There will be two files produced when using this `.ii` and `.s`. 

`.ii` stores the output of the preprocessing stage, definitely try that out to take a look at it, it does look very weird. 

The `.s` will output the linguistic analysis stage, ready for the assembler stage. 

There we can identify bugs, such as incorrect `include` paths, or mistakes in defs that lead to improper `#ifdef`. 

##### Debugging Issues with Header File Inclusion
`-H` compile option. 
```
add_executable(debug hello.cpp)
target_compile_options(debug PRIVATE -H)
```
Then when we build, we will get a list of include dirs. 
Each line will be a path to a header. 

Might look something like this: 
```
. main.cpp // this will include 
.. a.h
... c.h // a.h includes c.h
.. b.h // still included in main
```

So we look at the `..` to know what is included in this. 

Then we get a list of include header files that we should include guard for. If we see one of our files here, then we should consider that. 

##### Providing Information for the Debugger
The machine code will just be raw and bare. 

Debuggers act as a bridge between the machine code, that is super difficult to read, literally impossible, and source code (the code that we write). 

In the release build, machine code will be highly optimized: removing unnecessary instructions : it will remove variable and debug info to make the program faster and smaller. 

During the debug configs, the compiler will add extra metadata that link machine code back to the source code. 

This metadata will help with debuggers, which lines of code correspond to specific machine instructions. 

Release configs will strip out to make program smaller and faster. 
If we were to run this through the debugger, it would be way harder to track which line maps to which source code. 

```
CMAKE_CXX_FLAGS_DEBUG contains -g
CMAKE_CXX_FLAGS_RELEASE contains -DNDEBUG
```
`-g` just means, add debugging information. 

The debug information generated in a Debug Build is stored in OS's native format, such as Stabs, COFF, XCOFF, DWARF. 
These formats will describe the mapping between machine code, and source code, and they are understood by debuggers like GDB. 
![[Pasted image 20241217010011.png]]
DWARF most commonly used in GCC on Linux systems. 

When we compile using `-g`, GCC embeds debug symbols in the DWARF format: 
```
g++ -g main.cpp -o main
// then we do something like this 
gdb ./main
```

Then we are running the program with debugger. 
The DWARF format here will be pout into the exe. 

When we have a release config, all the `assert()` stuff will be disabled. 
