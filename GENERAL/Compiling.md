All that our code files are is text files, then we need to turn them into object files, then we have to link, then we can get our machine code. 
Overall, we will have our .obj files, and we will have our .exe file somewhere at the end. 

We get an obj file for each of our cpp files, each of our translation units, of course, not our headers. 
There are no such things as files in Cpp, we are just giving our compiler code to translate. 

We can tell our compiler that `.hugo` files, need to be compiled as a `.cpp` file, and then it will understand and do exactly that. 

Files have no meaning, like they do in java. 

Compilers will be told that `.cpp` files are translation units and that they need to be translated. 
Each `.cpp` file doesn't need to become a `.obj` file, meaning that if we include them all into one `.cpp` file at the end, then only the one will translate into an `.obj` file. 

### PreProcessing
`#include #define #if #ifdef`
`#include` compiler will go to the file, then go to that file, copy and past everything in, really really simple. 

You can with visual studio, create various in-between files, eg. the obj, the asm, or binary files too. 

An important point in the ASM files is that each function actually have a signature. They look like a random string of chars that is important during the linking phase. 

[Good Website for Source]()

# Compilation
![[Pasted image 20240728201427.png]]Pre-processing - Dealing with `#include`s and `#define`'s, the production of this step is a "pure" ++ file. 
Compilation - Taking pre-processor file and turning it into an `.obj` file. 
Linking - taking the `.obj` files, from the compiler and creates either a library or an executable file. 

**Compilation** - Parsing the pure C++ code, and then converting it into assembly code. 
Then it invokes the underlying back-end, that assembles that code into machine code, creating an actual binary file in some format eg. ELF, COFF, `a.out`. These files will have the compiled code in them. 

Here we have symbols, names for things, and we do not have to define them in that file. 

As long as the source file is well formed, we do not have to worry about these linking problems, that's for the linker. 

We can compile files singularly, without having to recompile the whole thing. All we really need here is an `.obj` file. 

These can be put into an archive, which we know to be a static library, for easier reusing in the future. 

At this state we may get syntactical errors that we have made. 

## Linking
This will produce the final compilation output, from the object files that we have just generated. 
This output can be either a shared (or dynamic) library or an executable. 
Here we are addressing all the unfollowed symbols, to which we don't have an address for in the code itself, however, here we are changing all those for actual addresses to the actual functions, using signatures etc. 
If they are defined elsewhere, then our linker has to know about them. 
Here we may run into missing definitions, or duplicate definitions. 

We may get the error that we are not linking correctly we are not getting to the file with the definition in, and we are not getting definitions for things that we need them for. 

![[Pasted image 20240728202720.png]]
Remember all this, this is after the preprocessor code has been added in order to get the pure C++ files. 
The symbol table, that's where all the names for things that during the linking phase, we will add the actual addresses too are. 

### DLL EXE SLL
DLL are libraries that contain code that can be used by multiple programs at once. Meaning that they can be reused by different applications, promoting code modularity and reuse. 
They are not exe's on their own. 
However they will be loaded and used by an EXE, or even another DLL.
They are able to export functions, variables, and classes to other EXE's or DLL's. 

They will be loaded at runtime, either explicitly by the program or implicitly, where they are named in the exe's import table. 

This means that someone can update it, and then everyone will get the update for it, as it is loaded in at run-time of the APP. 

Static Libraries are not that, they are added to the app's file before compilation. They are used by the linker to create an exe or another static library. 
Their code is incorporated directly into the app, into the exe, they are ready steady cooked into it, once they're in they can't change and the whole app will need to be recompiled in order to get the updated version. 

- **Runtime vs Compile-Time Linking**:
    
    - DLLs are linked at runtime, which means they can be updated independently of the application.
    - Static libraries are linked at compile time, which means the executable contains all the code it needs from the static library.
- **Executable Independence**:
    
    - An EXE is an independent executable file that can run on its own.
    - A DLL cannot run on its own and must be used by an EXE or another DLL.
- **File Size and Memory Usage**:
    
    - Using DLLs can reduce the file size of an executable because the shared code is in a separate file.
    - Static libraries increase the file size of the executable since their code is directly included.
- **Code Sharing and Modularity**:
    
    - DLLs promote code sharing and modularity because the same DLL can be used by multiple applications.
    - Static libraries do not support sharing between running processes, as each executable gets its own copy of the library code.

### How an EXE Connects to a DLL
In source, we declare the functions, classes, or variables that are provided by the DLL 
For Functions we might typically use `__declspec(dllimport)` in the header file to indicate that the function is indeed defined in a DLL. 
At compile time, we use an import library (a `.lib` file) associated with the DLL in order to resolve symbols and their addresses. 

At run time, the OS loader will take care of loading the DLL into the processes address space. 
- **Implicit Linking** - The DLL is loaded when the application starts. The OS will find the DLL in the exe's import table and loads them. 
- **Explicit Linking** - the DLL is loaded manually at run time using the `LoadLibrary` or `LoadLibraryEx` function, then we get more control over the DLL is loaded. 

When it is loaded, our DLL will be founded in app's process address space. 
Resolving the addresses of the exported functions and variables. 
For implicit linking, the loader updates the import address table (IAT) of the executable, with the actual addresses of the functions and variables in the DLL. 
For explicit linking, we use the `GetProcAddress` in order to get the address of the function or variable it wants to use. 

eg. 
##### `DLL Header`
```
#ifdef MYLIBRARY_EXPORTS
#define MYLIBRARY_API __declspec(dllexport)
#else 
#define MYLIBRARY_API __declspec(dllimport)
#endif

extern "C" MYLIBRARY_API void MyFunction();
```

Now you see how all those weird symbols in files make sense. 
Then the source file looks a little more simple 

##### `DLL Source`
```
#define MYLIBRARY_EXPORTS
#include "MyLibrary.h"

void myFunction() 
{ 
	 // Implementation of the function
}
```

Then here's our executable code: 
```
#include "MyLibrary.h"

int main() { 
	MyFunction();
	return 0;
}
```

And that should work fine. 

This is working with implicit loading of the DLL, meaning that the executable puts the DLL in its table. 

##### `ExplicitDLL`
This is much more complicated, as our code is explicitly loading the DLL: 
```
#include <windows.h> 
#include <iostream> 

typedef void (*MyFunctionType)();

int main() 
{ 
	HMODULE hLib = LoadLibrary("MyLIbrary.dll");

	if(hLib){ 
		MyFunctionType MyFunction = (MyFunctionType) GetProcAddress(hLib, "MyFunction");
		if(MyFunction) { 
			MyFunction();
		} else { 
			std::cerr << "Function Not Found \n";
		}
		FreeLibrary(hLib);
	} else { 
		std::cerr << "DLL NOT LOADED\n";
	}
}
```


### Implicit Linking

**Pros**:

- Simpler and cleaner code.
- Automatic loading and unloading of DLLs by the operating system.
- No need to manually manage DLL handles or function pointers.

**Cons**:

- All DLLs are loaded at startup, which can increase the initial load time.
- Less control over which DLL version is loaded.

### Explicit Linking

**Pros**:

- Greater control over when and if a DLL is loaded.
- Can load different versions of a DLL at runtime.
- Can handle DLL loading failures more gracefully.

**Cons**:

- More boilerplate code.
- Must manually manage DLL handles and function pointers.