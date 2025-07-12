Replacing the plain text symbol declarations that are found in header files. 
Then they are precompiled to the intermediary binary, greatly reducing the build time. 

### What Are They?
What they are is, as of right now, unsuitable for the current wide audience, as they are not fully compatible with everything. And will take more work to get them going, then anything else. 
Not relying on them right now, for production grade stuff is fine. However, might work in our own projects, just for fun. 
At their core, they are a single source file, encapsulating the functionality of the header and implementation files into one coherent unit of code. There are two primary components: 
- **Binary Module Interface (BMI)**: serving a similar purpose to a header file but is in a binary format. Meaning they are not recompiled when consumed by other translation units. 
-  **Module Implementation Unit:** providing the implementation, definitions, internal details of the module. Not directly accessible outside the module, encapsulating the implementation details is important. 
They were introduced to reduce compile time, addressing some problematic aspects of the pre-processor and traditional header files. 
There is a good picture here for a project that is using header files. 

There are issues with this idea: 
- We need include guards, there are issues when we don't have them. 
- There is the idea of cyclical references that require forward declarations. 
- Small changes to headers calls for the recompilation of all translation units. 
- Preprocessor macros can be hard to maintain and debug. 

How are they written: 
```
export module math; 

export int add (int a, int b)
{ 
	return a + b;
}
```

We say that this module is named `math`. 

`export` -> meaning that we will be able to use this outside the module. 
The extension to these files are all different: 
```
.ixx MSVC Extension
.cppm for clang
.cxx is for GCC
```

Then to use, we need to import, starting to feel like `python` here, which is not bad at all. 

```
import math; 


#include <iostream>

int main()
{ 
	add(2, 2); 
}
```

Looking very similar to header files, however, using CMake the same will be rough and will not work at all. 

#### Enabling Experimental Support in 3.26 and Up
We must acknowledge with this, not to use these in a hard production environment. 
This must be used mostly for testing purposes. 
In order to do this we need to use something looking like : `CMAKE_EXPERIMENTAL_CXX_MODULE_CMAKE_API` and change that variable. 
If CMake newer than 3.26 and we are good. 

```
cmake_minimum_required(VERSION 3.26.0)
project(CXXModules CXX)

# turn on experimental API
if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.28.0)
	# Assume that C++ sources do import modules
	cmake_policy(SET CMP1055 NEW)
elseif(CMAKE_VERSION VERSION_GREATER_EQUAL)
	set(CMAKE_EXPERIMENTAL_CXX_MODULE_CMAKE_API
		"aa1f7df0-828a-4fcd-9afc-2dc80491aca7") # idk wtf is going on
... some other version
else()
	message(FATAL_ERROR "Version lower than 3.26.0 not supported")
endif()
```

Meaning we just support experimental per every version, which is a pain, but still worth for these modules baby. 

### Enabling for 3.28 and Above
At the moment I am currently running 3.30 and that's without a recent update as far as I'm aware. 

```
cmake_minimum_required(VERSION 3.28.0)
project(CXXModules CXX)
```

This will set that experimental policy `CMP0155` by default, when we set the version to that. 

Then we need to do something with the compiler settings: 
#### Compiler Settings
There are two global variables that we need to fix up in order to start using modules. 
```
# LibC++ has no support compiler extensions for modules
set(CMAKE_CXX_EXTENSIONS OFF) # disable unsupported extensions
set(CMAKE_CXX_STANDARD 20) # the required standard
```


##### Declaring C++ Modules
```
target_sources( math
	PUBLIC FILE_SET CXX_MODULES TYPE CXX_MODULES FILES math.cppm
)
```
Remember the file set idea, is the extension to the files: 

The new file set is : `CXX_MODULES`. 
This is default supported by default in 3.28. 

These modules are to be reused within other libraries (putting the modularity). They can even be used in other projects. 

```
add_library(math)
target_sources(math
	PUBLIC FILE_SET CXX_MODULES FILES math.cppm
)

target_compile_features(math PUBLIC cxx_std_20)
set_target_properties(math PROPERTIES CXX_EXTENSIONS OFF)

add_executable(main main.cpp)
target_link_libraries(main PRIVATE math)
```

#### The ToolChain
If we are using Ninja, then it needs to be 1.11 and newer. 
Visual Studio 17 2022 and newer. 

Our compilers will also need to output files in a certain format: therefore we are looking at: 
- Clang 16
- MSVC in Visual Studio 2022 17.4
- GCC 14

Then do all of this fancy stuff that we have done a million times before: 
`cmake -B <build tree> -S <source tree> -G "Ninja"`

Then  you might also need
`cmake -B <build tree> -S <source tree> -G "Ninja" -D CMAKE_CXX_COMPILER=clang++-18`

Then just build: 
`cmake --build <build dir>`



