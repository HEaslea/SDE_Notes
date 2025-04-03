
There is the idea that we have to structure our code in order for it to make sense, when we get better and better we will realise parts of the code that we want to partition, parts that we can create as static lib files. 

#### What Is It
It's a logical unit, focussing on a specific objective. 
They can depend on other targets. 
When they are generated, we can leverage the artefact created. 

They can be seen as recipes that a buildsystem uses to compile a set of files into another file. 

This can be a `.cpp` file into a `.o` file, or a bunch of `.o` files into a `.a` (static lib). 
They are the recipes that produce those files, they provide us with the info of what we need etc. 

They understand, as does CMake how a language creates an executable, so that we don't have to write out code in order to make that happen. 

`add_executable(app1 a.cpp b.cpp c.cpp)`
`add_library()`
`add_custom_target()`
All the commands defining targets require the name of the target to be provided. 
This is so that they can be used later on. 
`target_link_libraries()`
`target_sources()`
`target_include_directories()`


## Custom Targets
Targets are specific units that you want to build, such as an executable, lib, or a custom task. 
They are the logical building block of CMake. 
They are essentially objects that CMake manages during the build process. 
When defining one, we are generating a particular output: 
When we define one, we are asking of CMake to generate a particular output `.exe .dll or .a` or some other artefact. 
Then we specify what we are putting into that target in order to build it. 

`add_executable(MyProgram main.cpp)` They represent `.exe` target
`add_library(MyLibarry STATIC my_library.cpp)`
```
add_custom_target(
	geenrate_files
	COMMAND python3 generate_files.py
	COMENT "Generating Files"
) # these usually generate files are perform specific tasks
```
`add_library(MyObject OBJECT file1.cpp file2.cpp)` 
These are object files for compilation. 

The name is important, that's how we are going to reference that file. 

###### Target Properties
```
set_target_properties(MyProgram PROPERTIES
	CXX_STANDARD 20
	RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)
```

###### Target Linkage
`target_link_libraries(MyProgram MyLibrary)`
Specifying which libs or other targets should link to using the `target_link_libraries` command. Establishing dependencies between targets. 

In the python custom command above, we have the idea that we are using a command such as: 
It will execute the python script. 

Remember that in order to generate a **BuildSystem** we have to run `cmake -B <build dir>`. 

### Transitive Usage Requirements
If we have a dependency and a target, then the dependencies of that target might be passed to another, that depends on it. 
The idea is that we have `PUBLIC, PRIVATE, INTERFACE` and they are nothing like the C++ access modifiers. 
`PRIVATE` - will only affect the source that is using that dependency
`INTERFACE` - will only affect the destination that is using this target 
`PUBLIC` - will affect both the source and the destination. 


Imagine a delivery service. 
- `PRIVATE` - the delivery stays inside the warehouse, the target that owns it. 
- `PUBLIC` - the delivery goes to the warehouse and also to the customers (dependent targets "on the warehouse"). 
- `INTERFACE` - The delivery skips the warehouse but goes directly to the customers (dependent targets "on that before the warehouse"). 

`PRIVATE` for encapsulation, meaning that we are not creating dependencies when we don't want them. 
`PUBLIC/INTERFACE` - don't have to worry about manually linking its dependencies. 

```
add_library(LibraryA STATIC a.cpp) # we are creating a static lib in there
target_link_libraries(LibraryA PUBLIC Boost::Boost) # Boost is required for this and the libraries that depend on this

add_library(LibraryB STATIC b.cpp)
target_link_libraries(LibraryB PRIVATE LibraryA)
# B needs A, but its users don't

add_executable(MainApp main.cpp)
target_link_libraries(MainApp PRIVATE LibraryB) 
# MainApp links B, and automatically gets Boost due to the PUBLIC for boost ealier
# MainApp will not depend on A, however, as that a was a private dependency. 
```

![[Pasted image 20241205132135.png]]

Here we want to compile into a new static library. 

`target_compile_definitions` - Will add that `MY_LIBRARY_DEBUG` into the compiling

`target_include_directories` - anything using this target will have these added as well, all targets will inherit this include directory. 

`target_compile_options` - These extra flags are added to the compilation of `MyLibrary` - they are specified as `PRIVATE` - this won't affect any target that uses `MyLibrary`. 

`target_link_libraries` - This library will require to link with `Boost`, and all of the propagated targets will require that as well. 

#### What Really Are Targets
Looking at this means that **targets** are really **containers** for the **settings and configurations** needed for building that particular output. 
They also define how **targets link together**. 
#### Propagation of Properties
When we link one target to another, then we can propagate (spread) some of these properties to that new target, like include directories, compile flags, or link binaries. 

If we link publicly, then we are looking at passing this targets properties on as well. 
If we link privately, then only this library will be using that property. 
If we link interface (ially), then only the linked targets will use those properties, this target will not be using that. 
Each target has properties -> these can be further divided into `PUBLIC, PRIVATE, INTERFACE`. 


### Adding Dependencies
In order to make sure that things are built in the right order: 
```
# Define LibraryA
add_library(LibraryA STATIC src/libraryA.cpp)

# Define LibraryB that depends on LibraryA
add_library(LibraryB STATIC src/libraryB.cpp)

# Define Executable that depends on LibraryB
add_executable(MyApp src/main.cpp)

# Set build order: LibraryB must be built after LibraryA
add_dependencies(LibraryB LibraryA)

# Link the executable to LibraryB
target_link_libraries(MyApp PRIVATE LibraryB)
```



Here we see that we are creating LibA from some cpp file, then we are creating libB as well. 
Then we are creating an executable. 
Here we need to make sure that LibA will be built before LibA. 
Then we link that lib to `MyApp` in order to make sure that the app is built last. 


`MyApp --> Lib B --> Lib A` dependencies, however, `Lib A`  will be built due to these dependencies. 

#### Interface Libraries
`add_library(MyHeaderOnlyLibrary INTERFACE)`
Adding include directories to the interface target. 
`target_include_directories(MyHeaderOnlyLibrary INTERFACE include/)`
This means that `MyHeaderOnlyLibrary` does not depend on anything, it will not be compiled, however, everything that links or depends on it will be be dependent on all the files in include. 

`target_compile_definitions(MyHeaderOnlyLibrary INTERFACE MYLIB_DEFINE=1)`

Linking to an executable: 
`target_link_libraries(MyExecutable PRIVATE MyHeaderOnlyLibrary)`

Targets have properties, these properties can be public, private, interface to show how they will propagate to other targets that this one linked against. 

#### Built and Non-Built Targets

There are targets that build and produce artifacts (Built Targets): 
`add_executable(MyExecutable main.cpp)` is one of them. 

`add_library(MyLib STATIC lib.cpp)` `SHARED` for `dll` I believe. 

Targets that are not going to produce artifacts (Non-Built targets): 
**Interface Libs:**
`add_library(MyILib INTERFACE)`
No associated source files. 
**Alias Libs:**
`add_library(MyAlias ALIAS MyOtherTarget)`

The idea of these is that they are simply there to propagate properties. 

**When to know:**
1. Source Files: If a target is associated with source files (`.cpp`) is a built target. Interface libs lack source files. 
2. Type in `add_library`: `STATIC SHARED MODULE` are targets that are going to be built, `INTERFACE` are not going to be built. 
3. Output Artifacts: Built targets produce binaries (executables, libraries). 
4. PURPOSE : built targets represent actual components of your application : non built targets serve to organize and propagate build system metadata. 

### Object Libraries
They don't directly produced a final artifact like an executable or a traditional lib. 
They compile source files into **object files** that can later be included in other targets. 

`add_library(MyObject OBJECT file1.cpp file2.cpp)`
They do not produce standalone output artifacts. 
This means that we can compile these and have them used by two different executables. 

This is more efficient than adding the same source files to multiple targets: 
```
# without object libs : source files are compiled multiple times
add_executable(Target1 file1.cpp file2.cpp)
add_executable(Target2 file1.cpp file2.cpp)
# these will be compiled twice

# With object libs: compiled once and reused
add_library(MyObjectLib OBJECT file1.cpp file2.cpp)
add_executable(Target1)
add_executable(Target2)
target_link_libraries(Target1 PRIVATE MyObjectLib)
target_link_libraries(Target2 PRIVATE MyObjectLib)
```
It will produce multiple `.o` files. 



### Build Targets
##### `ALL` Keyword
This means that the custom target will specify that the target should be included in the default build process. 

```
cmake_minimum_required(VERSION 3.10)
project(ALL_Example)

# custom target not in all
add_custom_target(custom_no_all COMMAND echo "Not part of ALL")

#custom target in all
add_custom_target(custom_with_all ALL COMMAND echo "Included in ALL")
```

The Keyword `ALL` is used to make a custom target part of the default build process. 
Then when we build our project, only targets marked with `ALL` will run automatically. If it is not marked with `ALL`, you must tell CMake to specifically run it. 
```
add_custom_target(not_all_target COMMAND echo "Run me only if you ask!")
add_custom_target(all_target ALL COMMAND echo "Run me by default!")
```

This means that when we run `cmake --build`, all the `ALL` will run automatically. 

If we have other executables, or libraries that are all default in `ALL`, however, we can do `EXCLUDE_FROM_ALL`, to make sure that something is not built by default. 

`ALL`  is another property or marker, mostly for our custom targets that tells CMake: 
> "Include this target in the default build process" - when you run `cmake --build < build dir > `


##### Clean
Is a special implicitly built in target that removes all files generated during the build process. 

Typically when we want to rebuild from scratch. 

When we generate a build system, there will be automatically a clean target. As in we do not have to explicitly define that in our files. 
Running `cmake --build . --target clean` deletes all the files created by the build process: 
- Object Files (`.o`, `.obj`)
- Intermediate build artifacts
- Final binaries (exe's, shared libs)
It won't just delete everything, we need to make sure that we are denoting files with `BYPRODUCT`. 
We have to declare files in custom target `BYPRODUCT`, in order to make sure that they get cleaned up at the end. 
Clean will just clean the byproducts. 
Libs, Exe's are cleaned automatically, they do not require the `BYPRODUCTS` marker. 
However, the thing being is that custom files and custom targets are not going to be auto cleaned. 

`ALL` will not be propagated with the transitive command. 


##### Custom Targets
When we add them to `ALL`, they will be run every single time, or if we start depending on them for other targets. 

If the command does not have `OUTPUT` or `BYPRODUCTS` then CMake will have no way of knowing if the command has already done its job. 

If we have a dependent target on our custom command / target, then it will be run every time the dependent target is run. 

```
add_custom_command( 
	COMMAND echo "Running this every single time"
	COMMENT "This runs on every build because no OUTPUT is specified"
)
```

Here, CMake does not know if there are any outputs, therefore there is nothing to track. 

```
add_custom_command ( 
	OUTPUT generated_file.txt
	COMMAND echo "Hello , World!" > generated_file.txt
	COMMENT "This runs only if generated_file.txt is missing"
)

add_custom_target(generate_file ALL DPENDS generated_file.txt)
```
This will also run if the `generated_file.txt` does not exist, or is older than its dependencies. 

```
add_custom_target( always_run ALL
	COMMAND echo "This will run every time"
	COMMENT "Custom Targets always run because they don't have an OUTPUT"
)
```
This one does not track custom targets and don't track outputs, therefore, CMake will have to run every time. 

The overview is this: 
 - Custom Targets with `ALL` - Always run during the build process. 
 - With Outputs - CMake will check fi those outputs are up to date, if the outputs are out of date or missing, the custom command will run. 
 - Dependencies Between Targets - `add_dependencies(target_A target_B)` and `target_B` has a custom command with outputs: 
	 - If `target_B`'s outputs are outdated, `target_B` will run first. 
	 - if `target_A` depends on `target_B`, and `target_B` needs to be rebuilt (because its outputs are outdated), `target_A` will run after `target_B` completes. 

### `add_custom_command()`
Add a custom command that generates files or performs actions during the build. 
This is **triggered by specific targets** it's a step in the build process. 
It runs only when its outputs files are missing or outdated (based on timestamps) or when is explicitly invoked as as dependency for other targets. 
```
add_custom_command( 
	OUTPUT geenrated_file.txt
	COMMAND echo "This is a generated file" > generated_file.txt
	COMMEN "Generating generated_file.txt"
)
```
Defining a single action or file generation step. 
Typically invoked by another target, executing specific commands as part of the build. 
Can be tied to specific outputs that, when outdated, trigger the command to run. 
Can be added to the dependency graph when we have something depending on it. 
Via `DEPENDS` or `add_dependencies()`. 
### `add_custom_target()`
Creates a new custom target in the build system. 
A custom target is executed when the target is built. 
It is always part of the build process, it execution depends on the build system. 
Does not directly produce files unless it depends on custom commands or other targets that generate files. 

```
add_custom_target( run_my_scripot
	COMMAND python3 my_scripot.poy
	COMMENT "Running custom script"
)
```
Defines a whole target that can be built, just like exe's and libs. 
Depending on custom commands and other targets, does not, itself generate output unless specified by custom command. 

#### Custom Commands as Target Hooks
```
add_custom_command( TARGET <target>
	PRE_BUILD | PRE_LINK | POST_BUILD
	COMAND command1 [ARGS] [args1...]
	[COMMAND command2 [ARGS] [args2...] ... ]
	[BYPRODUCTS [files...]]
	[WORKING_DIRECTORY dir]
	[COMMENT comment]
	[VERBATIM] [USES_TERMINAL]
	[COMMAND_EXPAND_LISTS]
)
```
A hook generally in Cmake is a way to **hook** a mechanism to attach custom commands to a target's build lifecycle, eg. `PRE_BUILD` or after with `POST_BUILD`. 
`PRE_LINK`  will be after compiling, but before linking. 

```
cmake_minimum_required(VERSION 3.26)
project(command CXX)
add_executable(main main.cpp) 
add_custom_command( TARGET main POST_BUILD // do after the build
	COMMAND cksum
	ARGS "$<TARGET_FILE: main>" > "main.ck")
)
```
The end of this is for the **generator expression**. 

## `add_custom_target()` and `add_custom_command()`

`add_custom_target` Target that performs custom commands, not producing an output file. 
Used for higher level operations, grouping commands or triggering non file based operations. 
They are always triggered as they are considered always out of date. 
Very good for running scripts : building documentation : triggering other commands. 
```
add_custom_target(TargetName [ALL]
	COMMAND <command1>
	COMMAND <command2>
	...
	DEPENDS <dependent-targets-or-files>
	WORKING_DIRECTORY <directory>
	COMMENT "Custom Target Description"
)
```
`DEPENDS`  what our command will depend on not the other way around. 
```
add_custom_command(
	OUTPUT generated_code.cpp
	COMMAND python generate_code.py > generated_code.cp
	DEPENDS generate_code.py
	COMMENT "Generating Source Code"
)
```

It may seem counter intuitive that the target will not produce any output files. 
`add_custom_target` is a task runner, not producing anything at all, it's a higher level group or trigger for commands, that will most likely always run. 
eg. Run all tests, build docs, clean temp files etc. 

`add_custom_command` producing files. The main idea, is that we are creating something, and when it's needed. 
It's tied to the dependency graph, meaning that if something needs this file, then we will produce it. 

```
add_custom_command( 
	OUTPUT generated_file.cpp
	COMMAND python script.py > generated_file.cpp
	DEPENDS script.py
)

add_custom_target( MyTarget
	DEPENDS generated_file.cpp
)
```


```
add_custom_command( 
	OUTPUT <output1> [<output2> ...]
	COMMAND <command1> 
	COMMAND <command2> 
	...
	DEPENDS <dependent-files-or-targets>
	WORKING_DIRECTORY <directory>
	COMMENT "Custom Command Description"
)
```
![[Pasted image 20241210140936.png]]

```
add_library(enable_rtti INTERFACE)
target_compile_options(enable_rtti INTERFACE
	$<$<OR:$<COMPILE_ID:GNU>, $<COMPILER_ID:Clang>>:-rtti>
)
```
The idea here is that we are only propagating values and properties for targets that link to it. 
Then the target compile options to be added to targets that link to the `enable_rtti` lib. 
