### Static Libraries: 

Collections of compiled code, bundled into the final executable at the build time, they must be linked against, and the header must be accessible to our main executable. 
`.lib` on Win and `.a` on Linux (but can also be `.a` on windows as well), that's the object code I believe. 

The idea here is that we create a `.hpp` and a `.cpp` for implementation. The `.hpp` file is for interface. 
Then we have to compile into an `.o` the `.cpp` file, make sure that it includes the `.hpp` file and any necessary other includes as well that is used in those files. 

`g++ -c teststatic.cpp -o teststatic.o`
Then you want to turn it into an `.a` file: The KEY HERE IS THAT WE NEED THE WORD `lib` in front of this `.a` file. 
`ar rcs libteststatic.a teststatic.o`

Then you just link with `g++ main.cpp -L. -lteststatic`. 

### Dynamic Libraries
Loaded at run time and not compiled, "baked" into our executable. 
Reducing the size of our own executable. 
We just need the file to be there at run time. 

When multiple applications share common functionality, or when you want updates without recompiling the main application. 

Source code is a little weirder for DLL's and in all honesty, most of the time you will be using SL's. 

```
// myLib.cpp
#include <iostream> 
extern "C" __declspec(dllexport) void hello() 
{ 
	std::cout << "HELLO FROM DLL" << std::endl;
}
```

The `extern "C"` prevents C++ name mangling, making the function usable by other languages or compilers. 
`__declspec(dllexport)` marks the function for export in the DLL . 

Then compile that shit: 
`g++ -shared -o myLib.dll myLib.cpp`

There is more code there: 
however, the idea is the same, then in order to get that code working we actually have to load that file up. 
```
// main.cpp

typedef void(*testFunc)();

int main() 
{ 
	HMODULE hlib = LoadLibrary("myLib.dll");
	
	if(!hlib)
		std::cout << "COULD NOT LOAD LIB" << std::endl;

	testFunc testSay = (testFunc)();

	if(!testSay)
	{ 
		std::cout << "COULD NOT FIND FUNCTION" << std::endl;
		return 1;
	}

	testSay();

	FreeLibrary(hlib);

	return 0;
}
```

I'm not sure about all the other code yet, however, it's not that complicated. 

Then, that will load that lib, you don't need to do the whole `-L. -lspecificLib` in order to get this to work, I think it just does when we compile and link there. 


### Executables
Being the main programs that people run: 
At its core, the computer will only understand instructions (machine code). Every program, everything is just a collection of these instructions. 
`.exe` are compiled code that the operating system can run. 
- Instructions
- Data
- References: links to libs that will provide more functionality. 

Libs are just code modules: `.so` and `.lib` encapsulate reusable functionality. 
They save us from rewriting common tasks all the time. 
This could be where a game uses graphics libs for rendering. 
A database program uses a data processing library for managing large datasets. 

Static linking : combining these modules into the exe at compile time. The exe will contain everything it needs, but it will be larger. 
Dynamic : combining and leveraging use at run time. The exe will be smaller, and more programs can share the lib. 

Layering Abstraction: 
- Game calls libraries and graphics and sound. 
- Those libs call system APIs provided by the OS. 
- The operating system manages hardware via drivers. 
Each layer hides complexity from the one blow it, making it more and more manageable with simple building blocks. 

###  The OS API
Is a set of functions and tools to allow programs to interact with the hardware and manage system resources. 
They provide a layer of abstraction between your application and the underlying OS. 
- System Calls:
	- Memory Allocation
	- File reading / writing
	- Process Creation
	- Device Management (keyboards, network interfaces)
	- Networking, opening sockets to send data and receive etc.
	
- Libraries and Headers: 

```
#include <unistd.h> // for system calls on unix like systems

int main() 
{ 
	// sys call: write "Hello world" to stdout
	write(1, "Hello world!\n", 14); // 1 is file descriptor for stdout
	return 0;
}
```

### All in CMAKE
Say that we have static libs that are in the `include` dir, then we have DLL's that are elsewhere on the system: 
```
# setting minimum version required
cmake_minimum_required(VERSION 3.10)

# Define the Project
project(MyProject)

# Set C++ standard 
set(CMAKE_CXX_STANDARD 20)

# Add include dirs (where the header files or static libs are located)
include_directories(${CMAKE_SOURCE_DIR}/include)

# add the path where your static libs are stored
link_directories(${CMAKE_SOURCE_DIR}/lib)

# Add a custom path for DLLs (runtime path)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Add the executable that you want to build
add_executable(MyExecutable main.cpp)

# Link static libs
# Assuming static libs are named `libstatic1.a` and `libstatic2.la`
target_link_libraries(MyExecutable
	static1
	static2
)

# Link DLLs 
# Assume dynamic libs are in another directory, such as `C:/libs/dlls`
link_directories("C:/libs/dlls")
target_link_libraries(MyExecutable
	my_dynamic_lib1.dll
	my_dynamic_lib2.dll
)

# If .exe is not in the same dir as the DLLs
# then set the runtime path to ensure that CMake looks for dlls at runtime: 
set_target_properties(MyExectuable PROPERTIES
	INSTALL_PATH "C:/libs/dlls"
)
```

```
find_package(OpenGL REQUIRED)

add_executable(MyProgram main.cpp)

# Link OpenGL to the exe
target_link_libraries(MyProgram PRIVATE OpenGL::GL)
```

or linking manually: 
```
add_executable(MyProgram main.cpp)

# Link to the static OpenGL lib
target_link_libraries(MyProgram PRIVATE opengl32.lib)
```
