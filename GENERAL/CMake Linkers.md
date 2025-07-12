Remember the five main stages - writing, compiling, linking, loading and execution. 
The object files, cannot be executed directly. 
They are just code files. 
Object files are a variant of the widely-used Executable and Linkable Format (ELF). 
These are common in Linux systems, however, MacOs and Windows have their own format. 

The structure of an object file looks something like this: 
```
// object_file.o

ELF HEADER -> General File Description

// NOW SECTIONS THAT COMPILED WITH SOURCE CODE FOR THIS TRANSLATION UNIT ONLY
.text
.data
.rodata
... and many more

SECTION HEADERS
```
The ELF Header, identifies the operation OS, file type, target instruction set architecture, and details on the position and the size of two header tables found in the ELF files : The Program Header tables and the Section Headers table. 
Then the binary sections that group information by type. 
A Section Headers Table, containing information about the name, type, flags, the destination addresses in memory, the offset of the tile, and other misc info. Used to understand what sections are in the file, just like a table of contents. 

When the compiler goes through the source code, it categorizes the gathered information into distinct sections. 
They form the core of the ELF file, positioned in the middle there, between ELF HEADER and SECTION HEADERS. 
`.text` -> contains machine code for processor execution. 
`.data` -> holds values for initialized global and static variables. 
`.bss` -> reserving space for uninitialized global and static variables, which get initialized to zero at the program's start. 
`.rodata` -> keeping read only data. 
`.strtab` -> string table containing constant strings, "Hello World" etc. 
`.shstrtab` -> section is string table holding the names of all other sections

These are close to the final `.exe` that gets put into RAM to run. 
We can't concatenate all the `.o` files to make an `.exe` just yet. 

Each section needs to be added to the corresponding sections of the other `.o` files. 
This is known as relocation. 

The ELF file type for `.o` is known as Relocatable.
This process will involve matching variable, updating internal references, symbol table indices, and string table indices. 
This means, as we add together, the number of the reference for each variable, and symbol etc. will be updated to the grand scheme of the whole `.exe`. 

page 205. 

Then we must have the cross referencing, whenever we `#include` or whenever we have `#extern`. 
The compiler will acknowledge these, hoping that later on, there will be a full conclusion on where that thing is. 
The linker will gather these unresolved external symbol references, and hopefully, past in all the actual references. 
Say that we have `f()` in `calling.o` however, `f()` is found in `external.o`. The linked `.exe` must have that fully resolved, obviously.

There are two things that might go wrong, no resolved references could be found, or there are too many references, and the linker is unable to choose. 

Then the final `.exe` will look very similar to the `.o` files, with all the relocated sections moved together: 
```
// exe file
ELF HEADER // general file description
PROGRAM HEADER // information for the loader

.text
.data
.rodata
... many more

SECTION HEADERS
```
The OS's loader will read that Program Header. 
It will read that to set up the program, configure memory layout, create a process image. 
It will specify which sections will be copied, in what order, and to which addresses in virtual memory. 
Each name section will be represented by one fragment of memory in the created process; such a fragment is called a segment. 

`.o` files might be configured and bundled into a library, which is an intermediate product that can be used in a final exe or another library. 

### Different Types of Libraries
What we don't want to have happen, is we write a whole thing, then in order to share, we share every single file that goes into our thing. This also means that we have to recompile all the things. 

Therefore, when we have our `.o`, we want to make it into a single unit, and then share that one. 

CMake will simplify this for us. 
We just use `add_library()`, pair that with `target_link_libraries()`. 

Common convention will be that we add the prefix `lib` to everything. 
Then we add the system specific extensions for each file. 

- Static lib `.a` on Unix and `.lib` on windows. 
- Shared lib (and modules) have a `.so` extension on UNIX, and `.dylib` on MACos  and `.dll` on windows
- Shared modules usually use the same extension as shared libs, can be different on MAC. 

### Static Libraries
A collection of raw object files stored in an archive, they can be sped up with index when linking. 

During the build process, only necessary symbols from the static lib are imported into the final executable, optimizing for size and memory usage. 
This also means that we have no need for external resources at run time, in this sense. 

`add_library(<name> [<source>...])`
or 
`add_library(<name> STATIC [<source>... ])`

This only links in to the one application. 

When we want to share a lib for multiple applications. 

### Shared Libraries
These are so different. 
These are completed with both stages of linking
- Static linking  : combines object files ad libs into a single exe, done at compile time, produces self contained binary
- Dynamic Linking : Links external libs at run time, not at compile time, sharing files without recompiling. 

Apps can share these shared libs. When the first program that uses them is loaded, the OS will load one instance of the lib into memory. 
Then subsequent programs will be given the same address. 
This means that the `.data` and `.bss` segments must be kept separate for every process, otherwise, what would be the point. 
Memory is optimized. 

If it's a major lib, we might not need to include it with our program, as it's likely available on the target machine. 

If not, then we need to grab it, and install it properly on the machine. 

This can lead to issues of course, and is a "dependency hell". 

`add_library(<name> SHARED [<source>...])`

There is little to no association between the executing program and the actual lib file on disk. 
The linking is done indirectly. 
On UNIX -> Shared Object Name (SONAME), which provides the name and the version. Helps the dynamic linker find and load the correct version of the lib at run time. 
Typically has the format `lib<name.so.<major_versoin>` `libexample.so.1`
Ensuring backwards compatibility, by allowing multiple version of a shared lib to coexist.  So just by adding the number, we can have multiple of the same type. 
The SONAME is stored in the ELF Header and set during compilation. 
The SONAME helps the dynamic linker to locate and load the correct version of the shared lib at runtime. 
During Compile Time, `libexample.so.1` for example, will be embedded into the binary as a dependency. 
At run time, the dynamic linker will search for the lib (matching the SONAME) in predefined locations, `/lib, /usr/lib` or directories in the `LD_LIBRARY_PATH`. 
Resolving the SONAME to the actual file. 
The SONAME is the just the logical version, not the actual version. 
![[Pasted image 20241217191929.png]]

In CMake we can query this property of the SONAME: 
```
$<TARGET_SONAME_FILE:target> // returns the full path (.so.3)
$<TARGET_SONAME_FILE_NAME:target> // returns only the file name
$<TARGET_SONAME_FILE_DIR:target> // returning the directory
```
That come in handy in more advanced scenarios. 

```
$<TARGET_LINKER_FILE:target> // returns the full path path to the .lib import lib associated with he produced dll 

$<TARGET_RUNTIME_DLLS:target> // returns a list of DLLS that the target depends on at run time. 

$<TARGET_PDB_FILE:target> // returns the full path to the .pdb program databise file (for debugging)
```

We need to know during compile time, which libs we are doing to use. 

### Shared Modules
This is a variant of a shared lib, designed to be used as a plugin loaded during run time. 
Unlike shared libs, that will be pulled up automatically, a shared module, only loads when the program explicitly requests it. 
This can be done through system calls: 
- `LoadLibrary` on Windows
- `dlopen()` followed by `dlsym()` on  Linux and macOS. 
Shared libs are linked at compile time with the SONAME (shared object name). 
These are loaded at run time, and run time only. 

- **Static Libraries**:
    
    - Code is **baked into the executable** at compile-time.
    - No external dependency at runtime.
    - Larger executable size.
- **Shared Libraries**:
    
    - Only the **names of the libraries (e.g., DLLs or .so files)** are baked into the executable at compile-time.
    - The **code** is located and loaded at runtime by the dynamic linker.
    - Reduces executable size and allows library updates without recompiling.
- **Shared Modules**:
    
    - Fully handled at **runtime**.
    - Not linked at compile-time; instead, they are loaded explicitly using functions like `dlopen()` (UNIX).
    - Commonly used for **plugins** or optional components.

`add_library(<name> MODULE [<source> ... ])`

### PIC
#### Position Independent Code
The idea of virtual memory, means that most code is position independent anyways. 

When we call a function for instance, we use the MMU (memory management unit), to translate the virtual to the physical page that the function will be in. 
These mappings don't always follow a specific order. 
PIC will not rely on hard coded addresses. 
Shared libs need to be used by multiple programs simultaneously. PIC means that a shared lib can be loaded at different memory addresses in different programs without needing recompilation or address relocation. 
**ASLR** - Address Space Layout Randomization - security feature that randomizes the mem location of executable code to prevent certain attacks, eg. buffer overflows, return orientated programming (ROP). PIC facilitates this by being relocatable. 
It introduces a new section to the binary files, **GOT** **Global Offset Table**. During the linking, the relative position of the GOT section to the `.text` section (the program code) is calculated. All symbol references will be pointed to through an offset to a placeholder in the GOT. 
When loaded, the GOT section will be transformed into a memory segment. 
Over time, it will accumulate the run time addresses of the symbols. 
This is called **lazy loading**, as we only populate GOT entries when required. 

All sources for shared libs and modules must be compiled with the PIC flag on. 
`POSITION_INDEPENDENT_CODE` `ON`. 
This is automatically enabled for shared libs. 
If the lib were to depend on another file, then we will need to turn that on for those as well: 
```
set_target_properties(dependency
			PROPERTIES POSITION_INDEPENDENT_CODE ON)
```

The main idea here is that we use relative addressing. 

Shared libs loaded into the address space of multiple programs. They might not have overlapping layouts. 

Each program has its own isolated memory, its address space. 
The shared lib contents (code and data) are mapped to specific part of this virtual memory. 
Then the code can call those functions as if they were their own code. 
Then at run time, the dynamic loader, will find the shared lib, map it into the program's memory, resolving the program's references to symbols. 

There is a slight overhead to this as well, as we have to work out the relative addressing. 

[Load Time Relocation of Shared Libs](https://eli.thegreenplace.net/2011/08/25/load-time-relocation-of-shared-libraries/)

Remember that shared libs, have a many names to them - shared libs, shared objects (like with SONAME), dynamic shared objects, (DSOs), Dynamically Linked Libs (DLLs - windows more often). 

##### Loading Exe's. 
Loading exe's to a fixed memory address. 
```
$ readelf -h /usr/bin/uptime
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  [...] some header fields
  Entry point address:               0x8048470
  [...] some header fields
```
Placed by the linker,  so OS knows where to start. 
Remember as well, that if we have a Debugger on the go, that uses DWARF formatting to map the machine code to the original source, there might be more information in there. 
A `exe` will have no relocation if there are no shared libs. These addresses do not need relocation as their addresses are based on predefined base address determined by the linker. These are virtual addresses. 

This means the linker can assign freely, as the memory addresses will just be mapped. 
The linker decides these virtual addresses. 

```
int glob = 42; 
int ml_func(int a, int b)
{ 
	glob +=  a; 
	return b + glob;
}
```
Notice that we are referencing `glob` a few times, the `.mov` instruction in ASM will need an address, which will require and absolute address. The linker does not know where to put this address. 

The dynamic loader, responsible for preparing programs for running. 

When a shared lib is loaded into memory, adjusted for its newly determined load location. 

1. Load Time Relocation 
2. Position Independent Coce (PIC)

Creating a shared lib requires this PIC - `-fPIC` (which is one by default for shared libs). 
```
gcc -g -c ml_main.c -o ml_mainreloc.o
gcc -shared -o -libmlreloc.so ml_mainreloc.o
```

Load Time Relocation - means that we have to run more at run time, we have to work out the overall address of the symbols. 
This is why there can be some start up slow down in the opening of an app. 

The point of the shared libs in general is that of saving RAM. The idea that we run into with Load Time Relocation is that we are taking the `.text` portion of the file and moving it into the new process. 
This is not the PIC approach, which prevents this. 
The LTR is one way, PIC is another. 
PIC is really what puts the share in shared library. 
with PIC there is an additional level of indirection to all global data and function references in the code. 
Utilizing some artifacts of the linking and loading processes, we can make the `.text` section of the lib truly independent. 
One thing that we rely on here is the offset between the text and data sections of the code. Known to the linker at link time. 
We know that when we link several object files together, those sections are relocated into the same sections of the other ones. 
The linker will know about the size and offset of the sections and their relative locations. 

![[Pasted image 20241219154217.png]]
`.text` and `.data`. 

Remember that the `x86` requires absolute addresses. 
When we have the relative address and the absolute address of the start, then we are just missing the instruction pointer. 

**Global Offset Table** - GOT-
Table of addresses residing in the data section. 
All symbol references will be pointed to through an offset to a placeholder in the GOT

THE GENERAL IDEA:
- PIC : uses relative addressing and doesn't depend on fixed virtual addresses, so it works regardless of where the lib is loaded in virtual memory. 
- LTR : Relocates absolute virtual addresses in the library to align with the process's address space. 

## Solving Problems with ODR
"There are two hard things in computer science : cache invalidation and naming things" - some dude at some point. 

Symbols must only be defined once. 
Declared and defined are different things, multiple declarations are fine. 
Declaring points to the definition, providing the idea that we have defined it. 
During linking, this is extended to the entire program. 
Inlined functions are not part of this
Yes, you can have **multiple inlined functions with the same definition** in your code, but there are some important nuances to understand:

You can define multiple times however, there must be the same definition for each. 

This is typical of template functions, as they can be instantiated in multiple places. Remember that templates are blueprints, that just generates code. They are not classes and functions in of themselves. 
They are typically defined in the head, so that the blueprint definition is found whenever it is needed. 
They are not affected by the ODR the same way. 
Each instantiation is a distinct entity. 
The linker will resolve instantiations as long as they are identical. 

Types, Temples and extern inline functions are exceptions to the rule, as they are away more complicated. 
However, only if they are perfectly identical. 
Remember, that's why we declare the class in header file, and define the function members in the cpp. 

`static` is local to the translation unit. 

Now this has to work slightly differently when we have shared libs. 

### Duplication in DLLs

```
// a.cpp
void a() 
{ 
	std::cout << "A" << std::endl;
}

void duplicated() 
{ 
	std::cout << "DUPLICATED A" << std::endl;
}
```


```
// b.cpp
void b() 
{ 
	std::cout << "B" << std::endl;
}

void duplicated() 
{ 
	std::cout << "DUPLICATED B" << std::endl;
}
```

```
extern void a();
extern void b();

extern void duplicated();

int main() 
{ 
	a();
	b();
	duplicated();
}
```

Let's see when we link them at different times. 

```
cmake_minimum_required(VERSION 3.26)
project(Dynamic CXX)
add_library(a SHARED a.cpp)
add_library(b SHARED b.cpp)
add_executable(main_1 main.cpp)
target_link_libraries(main_1 a b)
add_exectuable(main_2 main.cpp)
target_link_libraries(main_2 b a)
```

The output will be: 
```
A 
B 
DUPLICATED A

A
B 
DUPLICATED B
```

The order in which they are linked matters to the linker. 

Remember the idea of DLL Hell, when we have conflicting DLL's. 
Applications relying on different versions of the same shared lib. 
This is somewhat resolved by SONAME, these are baked in the executable, so that the exe can grab those DLL's when run. 

Local symbols will take precedence over the DLL's. 

Take care when using shared libs and conflicting names, they will crop up sooner rather than later. 

The real thing, just use NameSpaces: 
### Use Namespaces - Don't Count on the Linker
ODR is more easily dealt with. 
Just use a namespace after the lib if you want. 

We might find a link, where one lib links to another and another etc. 

Symbols will remain in their own namespaces throughout the chain. 

### The Order of Linking and Unresolved Symbols
This is a massive reason why novice programmers do not build anything from the get go, so complicated. 
Then we open pandora's box and have to have a go when we want to build something proper. 

Imagine that we have some chained libs, our exe relies on a, that relies on a nested b. 

Typically, this means a forgotten lib in the linker. 

So imagine that we have linked everything correctly. 

```
cmake_minimum_required(VERSION 3.26)
project(Order CXX)
add_library(outer outer.cpp)  # static lib by default
add_library(nested nested.cpp) # static lib by default
add_executable(main main.cpp)
target_link_libraries(main nested outer) # nested done first
```

What we are seeing here is an incorrect order of linking : yes, sadly this is a thing. 

The linker will process from left to right. 
They will resolve using binaries that we have seen so far. 
Here, we look at `main.o` and see that `a` is unresolved, therefore, we save it for later. 
Then it will process `libnested.a`, no undefined references were found. 
Then the `libouter.a` will find `a`, however, found an undefined reference to `b`. 

So then just reverse the order
`target_link_libraries(main outer nested)`

Then there is cyclic references, where translation units define symbols for each other, and there's no single valid order where all the references will be satisfied, that is something that we have come across. 
The only way to do this is to restate them: 
`target_link_libraries(main nested outer nested)`. 

The alternative is using `$<LINK_GROUP>` with the `RESCAN`, making sure all symbols are evaluated: 
`target_link_libraries(main "$<LINK_GROUP:RESCAN,nested,outer>)`

This is super rare, just something to remember when the time is right. 

### Dealing with Unreferenced Symbols
There is the idea that there are unreferenced symbols.  They are `functions, variables, objects` defined in the code or lib, that are never used or called in the program. 
Modern compilers often optimize these away, reducing the binary size (called dead code elimination). 

We can ask that the linker does not do this
`--whole-archive`
Some compilers will issue warning for unused variables, `-Wunused-variable` `Wunused-function`. 

`--force-load` for Clang

Again target link options to the rescue: 
```
target_link_options(tgt INTERFACE 
	-Wl, --whole-archive $<TARGET_FILE:lib1> -Wl, --no-whole-archive
)
```
`-Wl` telling the compiler to pass this on to the linker `ld` .
This means that the whole lib from `lib1` , regardless of whether there are references that are used or not. 
Then after `--no-whole-archive` the linker will resume as normal. 

Remember that a symbol is a name for a function, variable, class, or object, that the compiler or linker use to manage and resolve references in code. 

Luckily for us, there is the generator expression: 
```
target_link_libraries( # not options
	tgt INTERFACE
	"$<LINK_LIBRARY:WHOLE_ARCHIVE, lib1>"
) 
```
Remember that generator expressions are used to create these expressions that are otherwise really annoying to type out, and are not that portable onto the host system. 

`$<LINK_LIBRARY:WHOLE_ARCHIVE,lib1>` is an abstraction that works across platforms. 
And remember that this does not use `target_link_options` but `target_link_library`. 
Also easier to read, and isn't several variables away to understand. 

Remember however: that this means that there is a:
- increased binary size
- potential symbol clash : however, with namespaces this should be fine
- Maintenance overhead : although not very clear at the moment

## Preparing For Tests
We need to adhere (well the linker does) to the ODR and that every symbol that we could use, has the definition found. 
The ideal scenario, that games do well, is that we should be using the same source code that we have in the production, for testing. 

Executables typically have a defined flow, often involving the reading of command-line arguments. 

C++ doesn't really allow us to plug and play with binaries, say just for testing. 

Consider this idea: 
```
extern int start_program(int, const char**)
int main(int argc, char** argv) 
{ 
	return start_program(argc, argv);
}
```
Here we have delegated to another function, which is what we should usually do tbf. 
```
// program.cpp
#include <iostream> 
int start_program(int argc, const char** argv)
{ 
	if(argc <= 1)
	{ 
		std::cout << "Not enough args" << std::endl;
		return 1;
	}
	
	return 0;
}
```

Now building these might look something like this: 
```
cmake_minimum_required(VERSION 3.26)
project(Testing CXX)
add_library(program program.cpp)
add_executable(main main.cpp)

target_link_libraries(main program)
```

Making a static lib I believe, then we are just referencing to it. 
Then there is the idea of writing another `.cpp` like `test.cpp` for the test cases, that use the same `start_program` that we just interchange with CMake, however, Peter Muldoon, does not really like this all too much. 


