**Protobufs** (Protocol Buffers) , language neutral, platform neutral, mechanisms for serializaing structured data, developed by google . 

They use `.proto` files, that define the structure of the data. 
```
syntax = "proto3";

message Person 
{ 
	string name = 1;
	int32 id = 2;
	string email = 3;
}
```
They generate code in `C++` from these `.proto files`. 

The way that we do it, using a `.proto` file, then we use the Protobuf compiler `protoc --cpp_out=. file.proto`

There are dependencies that are already on the host system. 
This will reduce downloads and compile times. 

The idea is that we will look for the package in the host system first, then if we really need to, then we will download the package from either HTTP or Git. 

### Popular Libraries
When we look at a popular lib, it might already be on the system somewhere. 
We just have to connect it up. 
Paths working on one environment, may not work on another. 
Therefore, we need to find these paths. 
We can use the `find_package()`. 
This thing knows how to find most popular packages. 

If the package is not found within that support (not within the supported package). 
There are two options, if this `find_package()` does not find the package: 
 - We can write a small plugin called `find-module` to help it out. 
 - We can use an older method such as `pkg-config`


#### `find_package()`
There is the assumption that the package is on the system, duh. 
Take the `protobuf` example, you can use `git` in order to grab the repo. 
Or, if you have a package manager on your os, then just use that, `winget, chocolatey, NuGet` etc. etc. 
`apt` for Linux: 
```
apt update
apt install protobuf-compiler libprotobuf-dev
```
Many projects these days do us a favour so that they add a config file to the appropriate system directory during installation. 
The idea is that we add a config file into a system dir. 
This file will contain: shared settings, paths, or data. 
Typically on windows these are the `%ProgramData%` `%APPDATA%` or `%LOCALAPPDATA%`
On Linux, the common locations are /etc  (global settings)or `~/.config` (user specific). 
Any program can read these config files if it knows the location, enabling inter-process communication or shared functionality. 
System dir is a centralized resource containing the useful data. 
Example config file
```
install_dir=/usr/local/my_project
include_dir=/usr/my_project/include
lib_dir=/usr/local/my_project/lib
version=1.2.3
```

CMake can use these to locate dependencies during a build process, dynamically link in the specified directories, use custom configs for the project
There is this odd idea: 
```
file(READ /etc/my_project.conf CONFIG_CONTENT)
message("Config file contents: ${CONFIG_CONTENT}")
```
All users and processes can locate the project's configuration if we put it in the system dirs. 
This avoids hardcoding, meaning that we can change this config of truth at any point. 

Tools like package managers (apt, brew) often reference these files to manage installations. 

Finding a lib that doesn't have a config file, then we have to use something about **finding modules**. 

###### Find Modules
Locate libs, frameworks, software packages that aren't in CMake's built-in support. 
It's a custom script designed to locate a specific lib or tool and expose its variables for use in CMake projects. 

CMake looks for `Find<PackageName>.cmake` in predefined dir `CMAKE_MODULE_PATH` or default system paths. 
These scripts need to define how CMake will look for these libs or tools. 
OUTPUTTING VARIABLES - `<PackageName>_FOUND` indicating if the package was found or not. 
`<PackageName>_INCLUDE_DIRS` - Directories for include files
`<PackageName>_LIBRARIES` - libraries to link against

```
find_package(SomeLibrary REQUIRED)
if(SomeLibrary_FOUND)
	include_directories(${SomeLibrary_INCLUDE_DIRS})
	target_link_libraries(my_target)
endif()
```

Remember that `include_directories` will communicate with the preprocessing part of the compiler. 

Then we write our own Custom Find Module: 
```
# Try to locate the library
find_path(MyLibrary_INCLUDE_DIR NAMES mylibrary.h PATHS /usr/include /usr/local/include)
find_library(MyLibrary_LIBRARY NAMES mylibrary paths /user/lib /usr/local/lib)

# check if found
if(MyLibrary_INCLUDE_DIR AND MyLibrary_LIBRARY)
	set(MyLibrary_FOUND TRUE)
else() 
	set(MyLibrary_FOUND FALSE)
endif()

# provide results
mark_as_advanced(MyLibrary_INCLUDE_DIR MyLibrary_LIBRARY)
```

Then in order to use it: 
```
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
find_package(MyLibrary REQUIRED)
```

Then there are the built in ones: 
```
find_package(ZLIB REQUIRED)
```

Then just checking the output vars: 
```
<Package>_FOUND
<Package>_LIBRARIES
<Package>_INCLUDE_DIRS
```
```
if(ZLIB_FOUND)
	target_include_directories(my_target PRIVATE ${ZLIB_INCLUDE_DIRS})
	target_link_libraries(my_target PRIVATE ${ZLIB_LIBRARIES})
```

Then we can also specify versions as well 
`find_package(OpenSSL 1.1 REQUIRED)`

Remember this: That CMake will go through its built in packages before moving on to the config files that the different packages provide. 
CMake scans paths where packages are usually installed, depending on OS. 
It will look for these patterns: 
```
<CameCasePackageName>Config.cmake
<kebab-case-package-name>Config.cmake
```

If we want to add our own external find modules to the project, then we set the `CMAKE_MODULE_PATH` var. CMake will scan this first. 

##### Protobuf
In this example, we are going to use `Protobuf`.
The idea is that this lib saves data in a specific binary format, so that we can write and read C++ objects to and from files and then over a network as well. 
Then setting this up we would have to use a `.proto` file: 
```
syntax = "proto3";
message Message { 
	int32 id = 1;
}
```
Then `protobuf` will come with a binary that will compile these into C++ sources and headers that we can use in the application. 
We would obviously need to add these to the compilation steps in our build process. 

```
#include "message.pb.h"

#include <fstream>

using namespace std;

int main() 
{ 
	Message m;
	
	m.set_id(23);
	m.PrintDebugString();
	fstream fo("./hellodata", ios::binary | ios::out);
	m.SerializeToStream(&fo);
	fo.close();
	return 0;
}
```
Here we are expecting `protobuf.ph.h` to be generated by `protobuf`. 

Giving us the definition for the `Message` object. 
Then the idea is that the binary version of this object is written to a file. 

This is the most basic use case for a lib for proto like this. 

We have to make sure that the file is produced before we compile `main.cpp`. 

This will have to be done by the protobuf compiler, `protoc`. 

```
cmake_minimum_required(VERSION 3.26.0)

project(FindPackageProtobufVariables CXX)

find_package(Protobuf REQUIRED)

protobuf_generate_cpp(GENERATED_SRC GENERATED_HEADER message.proto)

add_executable(main main.cpp ${GENERATED_SRC} ${GENERATED_HEADER})

target_link_libraries(main PRIVATE {Protobuf_LIBRARIES})
target_include_libraries(main PRIVATE ${Protobuf_INCLUDE_DIRS} ${CMAKE_CURRENT_BINARY_DIR})
```

`find_package(Protobuf REQUIRED)` - telling CMake to find the lib (executing the bundled `FindProtobuf.cmake` find module) and prepare it for use in our project. 
If it cannot find it, it will stop the build as we used the `REQUIRED` keyword. 
Typically the `.cmake` will be provided by the lib or installed with it. 
It will look for it in the `CMAKE_MODULE_PATH` or `CMAKE_PREFIX_PATH`. 
Usually that module will be in the `Modules/` dir of CMake installation. 
This module will also provide the `protobuf_generate_cpp` in order to do what it needs to do. 
However, it might also need to look in common locations and dirs. 
Config Package File : if Protobuf was installed with CMake
Find Module: Fallback if no config file is found, tries to locate Protobuf using environment variables, or manual hints like `CMAKE_PREFIX_PATH`. 

Find Modules are built into cmake, there are quite a few that will come with CMake I believe (`FindBoost.cmake`, `FindProtobuf.cmake`). 

Config files are usually installed alongside the lib itself, in standard dirs, or custom paths during the lib's build. 
They are usually placed in places: 
`C:\Program Files\<Library>\cmake\`. 

When the lib is installed, the config file is put in the appropriate system directory during installation. 
Find modules do come with CMake. 

Then we are just adding an `.exe` and adding the right source files.

Then we ned the `link_libs`. 
`target_link_directories()` : adding the include paths to the necessary `INCLUDE_DIRS` provided by the package and `CMAKE_CURRENT_BINARY_DIR`. 

Then the Protobuf Find Module gives us this functionality: 
- Finds protobuf lib and compiler. 
- Provides helper functions to compile the `.proto` files. 
- Setting variables with paths for inclusion and linking. 

Most of the find modules and config files will set some variables up for you: 
- `<PKG_NAME>_FOUND` indicating whether this was found or not
- `<PKG_NAME>_INCLUDE_DIRS or <PKG_NAME>_INCLUDES` : Paths pointing to the dirs
- `<PKG_NAME>_LIBRARIES or <PKG_NAME>_LIBS` : The List of libs that you'll need to link against. 
- `<PKG_NAME>_DEFINITIONS` :  This contains any compiler definitions needed for the package. 

Then we can just use the `<PKG_NAME>_FOUND` variables, to see whether the whole thing is successful. 

They may even provide target definitions; that will be `imported targets` : they are predefined targets that refer to libs or other dependencies that you want to use in your project, but are not built directly by your cmake project. 
Find Modules will use them a lot. 
If the lib is found, it will usually import target for that lib eg. for `OpenGL`: 
```
find_package(OpenGL REQUIRED)
target_link_libraries(my_target OpenGL::GL)
```

Then we use the idea of transitive usage requirements in order to pass whatever we need to other targets, aka the propagated properties: 
`target_link_libraries(main PRIVATE protobuf::libprotobuf)`
Just use the online documentation for all the find module bullshit. 

Remember that the main idea is that we utilise the `<PKG_NAME>_FOUND` in order to make sure that the user knows that the lib could not be found, and will install it if they can. 
Or, later we'll see how to install the package automatically: 

There are other args that we could use: 
`find_package(<NAME> [version] [EXACT] [QUIET] [REQUIRED])`
- `[version]` being the minimum version that we need. `major.minor.patch.tweak` this could be a range that uses the `...` eg. `1.22 ... 1.40.1`
- `EXACT` saying that the version has to be exactly the one stated. 
- `QUIET` This suppresses all messages about whether the package was found or not.
- `REQUIRED` the build will stop fi the right package is not found, will show this message even if `QUIET` is used.

If we feel sure that the lib should be on the system, yet we are not finding it, then we can run: 
`cmake -B <build tree> -S <source tree> --debug-find-pkg=<pkg>`

What do we do if we don't have a `find module` yet. 

### Writing Our own Find Modules

Then we need to write our own find module and ship it with the project. 

In the interest of our users, then we need to do it. 

We might write a file such as `FindPQXX`, and store it in the `cmake/module` dir within our project's source tree. 

Then in order to make sure that CMake will find that module during the `find_package()`, we need to make sure that it's added to `CMAKE_MODULE_PATH` in the CMakeLists.txt. 
We just do this with `list(APPEND)`. 
CMake will first check the dirs listed in that list before looking in other locations. 
```
CMakeLists.txt
cmake_minimum_required(VERSION 3.26.0)
project(FindPackageCustom CXX)

list(APPEND CMAKE_MODULE_PATH
	"${CMAKE_SOURCE_DIR}/cmake/module/")

find_package(PQXX REQUIRED)
add_executable(main main.cpp)
target_link_libraries(main PRIVATE PQXX::PQXX)
```

We, as authors, need to write to `FATAL_ERROR` if the package is not found. 
It is also up to the author in order to keep the module quiet. 

If we know the paths beforehand, perhaps the user has given them, or they are in the cache. 
Remember that the cache is just the storing of variables. 
Remember that we can do `set()` with the`CACHE` option to make sure that it will be reused over various runs. 
`find_package()` should really do this, as followed. 

If they are in cache, or the user provides them, then we can just use those, create a target. 
If not known, then we need to find the lib and headers. 

Then look at the well known paths to get the binary of that lib. 
Then use those paths as well to get the headers. 
If they are both located, then create the target. 

Looking at whether the user provides the paths, or we have to find them. 

### Defining Imported Targets
All we really need here is `IMPORTED` keyword in a lib; allowing us to use the `target_link_libraries()` in the caller. 
We do not mind if the target is a static or dynamic lib, we just need to pass an argument to the linker. 

You could even do things like set more `COMPILE_DEFINITIONS`, however, that isn't what we need here. 

Then we should store those found path variables in the cache so that we can use them again: one of these is that we have to change `PQXX_FOUND` in cache in order to use that later as well, this will make it global as well to make sure that we can use it in the caller as well. 

The weird thing is that we need to set these vars as `advanced`, hiding them in the CMake GUI. 

```
# Defining IMPORTED Targets
function(define_imported_targets library headers)
	add_library(PQXX::PQXX UNKNOWN IMPORTED)
	set_target_properties(PQXX:PQXX PROPERTIES
		IMPORTED_LOCATION ${library}
		INTERFACE_INCLUDE_DIRECTORIES ${headers}
	)
	set(PQXX_FOUND 1 CACHE INTERNAL "PQXX Found" FOCE)
	set(PQXX_LIBRARIES ${library}
		CACHE STRING "Path to PQXX library" FORCE )
	set(PQXX_INCLUDES ${headers}
		CACHE STRING "Path to PQXX headers" FORCE)
	mark_as_advanced(FORCE PQXX_LIBRARIES)
	mark_as_advanced(FORCE PQXX_INCLUDES)
endfunction()
```

`IMPORTED` - meaning that the lib is defined externally and not built as part of the current project. 
They are precompiled and external. 
This lets CMake know whether that lib already exists (provided as a binary file, or built elsewhere), and we are just using it in our project. 

It lets CMake know not to make that lib, and that we will provide details on where that file lib is. 

```
add_library(MyImportedLib STATIC IMPORTED)
set_target_properties(MyImportedLib PROPERTIES
	IMPORTED_LOCATION "/path/to/libMyImportedLib.a" # path to the lib
	INTERFACE_INCLUDE_DIRECTORIES "/path/to/include" #Include Directory
)
```

The `IMPORTED` here will allow CMake to assume that the lib already exists, and that we are not compiling it this time round. 

Remember that when we have a path to a lib that is a binary, that might look like `.a .so .dll .lib`. 

Then, to use that we just use something like `target_link_libraries(MyTarget PUBLIC MyImportedLib)`

#### Letting the User Give Paths in Command Line
When this occurs we immediately call that function above. 
We can only assume that the user has provided correct paths. 

```
# Accepting user-provided paths and reusing cached valued
if(PQXX_LIBRARIES AND PQXX_INCLUDES)
	define_imported_targets(${PQXX_LIBRARIES} ${PQXX_INCLUDES})
	return()
endif()
```
if this was configured previously, then this check passes. 

The host system has to have the PQXX lib installed. 

```
set(QUIET_ARG)
if(PQXX_FIND_QUIETLY)
	set(QUIET_ARG QUIET)
endif()

set(REQUIRED_ARG)
if(PQXX_FIND_REQUIRED)
	set(REQUIRED_ARG REQUIRED)
endif()
find_package(PostgreSQL ${QUIET_ARG} ${REQUIRED_ARG}})
```

Remember roughly how dependencies work: 
```
add_library(A ...)
add_library(B ...)
target_link_libraries(B PUBLIC A) # B depends on A

add_libraryc(C ... )
target_link_libraries(C PUBLIC B) # C depends on B and transitively on A
```

Then we can get those dependencies as such
```
get_target_property(deps C INTERFACE_LINK_LIBRARIES)
message(STATUS "C's Dependecies: ${deps}")
```

Therefore, when we have a dependency that uses an imported lib : then that might also have a dependency as well: 
```
find_package(Boost REQUIRED COMPONENTS system filesystem)
target_link_libraries(MyApp PUBLIC Boost::system Boost::filesystem)
```
`Boost::system` might depend on `Boost::core`

Remember too that we can debug dependencies using `cmake --grpahviz`

`cmake --grpahviz=output.dot /path/to/build`


##### Where is that File? 
#### Searching For Lib Files: 
CMake offers the `find_library(<Var_Name> NAMES <Names> PATHS <Paths> <...>)`
`<VAR_NAME>` being the name of the var that stores the command's output, if a matching file has been found, the path will be stored in the `<VAR_NAME>` variable. 
Otherwise the `<VAR_NAME>_NOTFOUND`  will be set to 1. 

```
# continued from the last file
# searching for library files
file(TO_CMAKE_PATH "$ENV{PQXX_DIR}" _PQXX_DIR)
find_library(PQXX_LIBRARY_PATH NAMES libpqxx pqxx
	PATHS
		${_PQXX_DIR}/lib/${CMAKE_LIBRARY_ARCHITECTURE}
	#(...) many other paths - removed for brevity
	/usr/lib
	NO_DEFAULT_PATH
)
```
`file(TO_CMAKE_PATH ... )` just converts a given file system path to a format compatible with CMake's expectation. 

`file(TO_CMAKE_PATH input_path output_variable)`

Typically windows looks like `C:\path\to\file` and then does this to it `C:/path/to/file`. 

This just means that we can do anything with the paths that we put in, and it will always work when we put it through this conversion: 
```
set(original_path "C:\\path\\to\\file")
file(TO_CMAKE_PATH "${original_path}" converted_path)
message(STATUS "Converted Path: ${converted_path}")
```
That will look like `C:/path/to/file`.

You generally would not want to do `NO_DEFAULT_PATH`, as it bypasses the paths that CMake can use, which are usually correct. 

#### Searching For Header Files
`find_path()` works similarly to the `find_library()`: 
`find_library()` Common used to find library: put in a cache entry, or a normal variable if `NO_CACHE`, named by `<VAR>` which will store the command. 
The search will not be repeated unless that variable is cleared. If nothing is found, then `<VAR>-NOTFOUND` will be true. Will also append system specific extension for these libs, so that we don't have to worry about it as authors.

`find_path()` Finding the dir that will contain the named file: Similarly, the cache entry or a normal variable with `NO_CACHE`. They are pretty much the same. 
However, we don't have the luxury as the system specific extensions that we do with `find_library()`.

```
#searching for header files
find_path(PQXX_HEADER_PATH NAMES pqxx/pqxx
	PATHS
		${_PQXX_DIR}/include
	# (...) many other paths - removed for brevity
	/usr/include
	NO_DEFAULT_PATH
)
```
Meaning that if we find the result, and it is found in `/usr/include`, then put it in the output variable `PQXX_HEADER_PATH`. 
`NAMES` name the file that we are looking for, `pqxx/pqxx` means that we are looking for a file in `pqxx/`.
`/usr/include` is a standard system path where libs and headers are commonly located. 

#### Returning the Results
Remember that we have to check whether these libs or headers are found. 
Then we can either print manual messages to say that, or we can use this whole function: 
`find_package_handle_standard_args()` helper function from CMake. 
This will set everything properly, and will also output the diagnostic message that we need. 
Weirdly, it will respect the `QUIET` idea that we showed earlier. 
Will also just halt building if we had used `REQUIRED` in the `find_package()` invocation. 

If we have found the two, then we can use the function that we wrote earlier in order to write the defined target. 
We want to store the paths in CACHE as well, just so we don't have to run the whole thing again. 
```
# returning the final results
include(FindPackageHandleStandardArgs) # just like import
find_package_handle_standard_args(
	PQXX DEFAULT_MSG PQXX_LIBRARY_PATH PQXX_HEADER_PATH
)
if(PQXX_FOUND)
	define_imported_target( # the function that we used before
		"${PQXX_LIBRARY_PATH};${POSTGRES_LIBRARIES}"
		"${PQXX_HEADER_PATH};${POSTGRES_INCLUDE_DIRECTORIES}"
	)
elseif(PQXX_FIND_REQUIRED)
	message(FATAL_ERROR "Required PQXX Library Not Found")
endif()
```

The `define_imported_target` function that we created earlier: 
```
function(define_imported_target library headers)
	add_library(PQXX::PQXX UNKOWN IMPORTED)
	set_target_properties(PQXX::PQXX PROPERTIES
		IMPORTED_LOCATION ${library}
		INTERFACE_INCLUDE_DIRECTORIES ${headers}
	)
	set(PQXX_FOUND 1 CACHE INTERNAL "PQXX FOUND" FORCE)
	set(PQXX_LIBRARIES ${library}
		CACHE STRING "Path to PQXX Lib" FORCE)
	set(PQXX_INCLUDES ${headers}
		CACHE STRING "Path to PQXX headers" FORCE)
	mark_as_advanced(FORCE PQXX_LIBRARIES)
	mark_as_advanced(FORCE PQXX_INCLUDES)
endfunction()
```
Remember that an imported target is one that CMake does not build. 

Remember also that when we have `CMakeLists.txt` in other dirs, then we need to use `add_directory` in order to add that dir as a subproject: 
Targets that are defined in the project are then available in the parent scope. 
```
add_subdirectory(subdir)
target_link_libraries(MyExecutable PRIVATE SubDirTarget)
```
Then `include()` will just run that file as if it were in the main file. 
This does not make a separate scope. 
`include(common.cmake)` - I guess more useful for scripting as well. 
This means that we can use the targets that are defined in other dirs: 
in `subdir/CMakeLists.txt`
```
add_library(SubDirLib STATIC lib.cpp)
```

Then in the parent `CMakeLists.txt`
```
add_subdirectory(subdir)
target_link_libraries(MyExecutable PRIVATE SubDirLib)
```
We can even export targets to another file, for use in external projects: 
```
install(TARGETS MyLibrary EXPORT MyLibraryTargets DESINGATION lib) # which will be explained later in the page and the book
install(EXPORT MyLibraryTargets DESTINATION cmake)
```

![[Pasted image 20250104231723.png]]
#### Older Libs
When there are older libs, we are going to struggle with this method. Then we need use something a bit more modularized: 
Such as `pkg-config` which CMake has a wrapper for very nicely. 
The main idea here though is that we shouldn't really be putting so much into something like this, just older packages 

### Using Dependencies When They Are Not Present On The System

The `FetchContent` module is the one for us. 
This is a user friendly module over the `ExternalProject` module, which is a little more fucked. 
`FetchContent` brings in dependencies during the **Configuration** stage;
`ExternalProject` brings in dependencies during the **Build** stage;

This is quite a bit deal I believe: 

#### FetchContent
This brings in externals and : 
- manages a dir structure for an external project
- Downloading of sources from a URL (and extracting archives if need be. 
- Support for Git, Subversion and Mercurial and CVS (Concurrent Versions System) repos
- Fetching updates if need be (that's mad)
- Configuring and building the project with CMake or Make, or other tools. 
- Providing nested dependencies on other targets. 

There are three main steps: 

1. Add the module to your project with the `include(FetchContent)`
2. Configure the dependencies with the `FetchContent_Declare()`. This will instruct `FetchContent` where the dependencies are and which version should be used. 
3. Complete the dependency setup using the `FetchContent_MakeAvailable()` command, this will download, build, install and add the listfiles to your main project for parsing. 

###### Configuration Over-rides
Consider a project that depends on external libs, A and B. Lib A also depends on B, its authors are using an older version that differs from the project's version. 

```
	      -> lib A - > Lib B(v1)
		 |
Project -|
		 |
		 -> lib B (v3)
```

The idea being that `FetchContent_Declare()` uses the old `ExternalProject_Add()` under the hood. 

#### Example Using YamlReader
YAML will store human readable configurations. However, machines do struggle with it. 
The `yaml-cpp` by Jesse Beder, does as much for us as it can. 

```
#include <string> 
#include <iostream> 

#include "yaml-cpp/yaml.h"

using namespace std;

int main() 
{ 
	string name = "Guest";

	YAML::Node config = YAML::LoadFile("config.yaml");

	if(config["name"])
		name = config["name"].as<string>();
		
	cout << " Welcome " << name << endl;
	return 0;
}
```

The yaml file might look something like this: 
```
name: Rafal
```

Then building it in CMake: 
```
cmake_minimum_required(VERSION 3.26.0)
project(ExternalProjectGit CXX)

add_executable(welcome main.cpp)

configure_file(config.yaml config.yaml COPYONLY) # just copying from files here

include(FetchContent) # so we can use the helper function to bring in other modules

FetchContent_Declare(external-yaml-cpp
	GIT_REPOSITORY        https://github.com/jbeder/yaml-cpp.git
	GIT_TAG               0.8.0
)

FetchContent_MakeAvailable(external-yaml-cpp)
target_link_libraries(welcome PRIVATE yaml-cpp::yaml-cpp) # remember that this is how imported targets are returned
```
`COPYONLY` -  Not replacing any variable references or other content. 

Using another module in order to print out something about that target that we just received: 
```
include(CMakePrintHelpers)
cmake_print_properties(TARGETS yaml-cpp::yaml-cpp
	PROPERTIES TYPE SOURCE_DIR)
```

Which will print: 
```
Properties for TARGET yaml-cpp::yaml-cpp
	yaml-cpp.TYPE = "STATIC LIBRARY"
	yaml-cpp.SOURCE_DIR = "/tmp/b/_deps/external-yaml-cpp=src"
```

Remember to just read the documentation when using something, I feel as thought that is the biggest take away here. 

We use `configure_file()` in order to copy the right `.yaml` file into the right place. 

If we need multiple dependencies, then we need to write a few more `FetchContent_Declare()`, remember that every time we need to use a unique identifier, otherwise the new records are not going to overwrite anything, they just will be ignored, look at the documentation. 

However, `FetchContent_MakeAvailable()`  just needs the one call: 
`FetchContent_MakeAvailable(lib-A lib-B lib-C)`

##### `FetchContent_Declare`

We can: 
- Download Dependencies
- Update Dependencies
- Patch Dependencies

Fetching Files from our beloved internet: 
- HTTP server (URL)
- Git 
- Subversion (SVN)
- Mercurial
- CVS (Concurrent Version System)

###### Downloading From URL
We provide a list of URLs that can be scanned in sequence until a download succeeds. CMake will understand whether the download file is an archive and will unpack it by default: 
**WHAT IS AN ARCHIVE?**: These are the lovely `.zip` , Tarballs: `.tar, .tar.gz, .tgz, .tar.bz2` and then 7-Zip files (`.7z`). 

`.tar.gz` is a combination of two file compressing techniques: `TAR` and `GZIP`. 
CMake will automatically recognize the archive format and unpack it into the directory that we want to use. 

```
FetchContent_Declare(dependency-id
	URL <url1> [<url2> ...]
)
```

There are the further options as well. 
Using `URL_HASH` in order to checksum and guarantee the integrity of downloads. 
You can download without extracting. 

`TIMEOUT` - if not download succeeded in that time, and due to inactivity, then just abandon. 
Remember TLS - transport security layer - CMake will also  check that for us if we ask really nicely. 

**GitHub**
Most of the time, we are just going to use GitHub, we love that shit: 

We must ensure that we have Git version 1.6.5 on the host system: 
```
FetchContent_Declare(dependency-id
	GIT_REPOSITORY <url> # both these have to be compatible with the git command
	GIT_TAG <tag>
)
```

Using a git hash, that hash being from the commit that we have found. 
Tags are human readable, commits are just insane to read. I think they use `SHA-1`. 

```
FetchContent_Declare(
    MyLibrary
    GIT_REPOSITORY https://github.com/example/mylibrary.git
    GIT_TAG 4b825dc642cb6eb9a060e54bf8d69288fbee4904 # Refers to a specific commit
)

FetchContent_Declare(
    MyLibrary
    GIT_REPOSITORY https://github.com/example/mylibrary.git
    GIT_TAG 4b825dc642cb6eb9a060e54bf8d69288fbee4904 # Refers to a specific commit
)
```

`CI/CD` - contiguous integration / continuous delivery. 

The idea of a shallow get in Git: 
`git clone --depth 1 <repository-url>`. This will clone only the latest commit (most shallow). 
Then `--depth N` this will clone the last N commits. 
Meaning that the `.git` directory will contain that history of those 5 commits. 
This means that `git.log` will show those commits. 

The `.git` dir is the hidden dir in a git repo, containing all the data Git needs to track that history of our project. 
Data about: commits, branches, configuration, and more. 

**Subversion and Mercurial**: 
Basically the same shit: 
```
FetchContent_Declare( depedency-id 
	SVN_REPOSITORY <url>
	SVN_REVISION -r<rev>
)
```

```
FetchContent_Declare( depedency-id 
	HG_REPOSITORY <url>
	HG_TAG <tag>
)
```

**From CVS** Concurrent Version System(Software): 
```
FetchContent_Declare( dependency-id
	CVS_REPOSITORY <cvsroot>
	CVS_MODULE <module>
	CVS_TAG <tag>
)
```


##### Updating and Patching
Update will redownload the external project's files if the download method supports updates eg. If we configure the git dependency pointing to the main or master branch.




#### AVOID HARDCODING CREDENTIALS

1. Environment Variables : `export DB_PASSWORD="mypassword"`
Then in code: 
```
# python
import os
db_password = os.getenv("DB_PASSWORD") 
```

2. Config Files : 
However, just don't submit it to version control: meaning that we put it in the `.gitignore`

```
/config.json
{ 
	"username" : "admin"
	"password" : "some_password"
}
```

```
#python
import json

with open("config.json") as config_file: 
	config = json.load(config_file)
db_password = config["password"]
```

Just add it to the git ignore!!!: 
`echo "config.json" >> .gitignore`


There are other ways: 
![[Pasted image 20250106023239.png]]
There is also the idea that we could just decrypt them at run time, however, someone might just be a dick enough to figure that one  out. 

Updating: The idea that you could just change the `GIT_TAG` for instance. 
The thing is that if we point to the main branch of a git repo, then it will auto update. 
If CMake has already fetched the dependency, you may need to clear the cache to force a refetch: 

Getting the latest git version every time means that we have to use `GIT_TAG` `main`: 
Then set the `UPDATE_COMMAND` to pull the latest changes: 
```
FetchContent_Declare( 
	MyLibrary
	GIT_REPOSITORY https://github.com/example/mylibrary.git
	GIT_TAG main # Always fetch the latest commit from the 'main' branch
	GIT_PROGRESS ON
)

# Fetch the Content and update automatically
FetchContent_MakeAvailable(MyLibrary)

# Ensure the altest commit is always pulled
FetchContent_GetProperties(MyLibrary)
if(NOT MyLibarry_POPULATED)
	FetchContent_Populate(MyLibrary)
	execute_process(
		COMMAND git pull origin main
		WORKING_DIRECTORY ${MyLibrary_SOURCE_DIR}
	)
```

`PATCH_COMMAND` will specify the command that we want to use, after the source code has been downloaded but before it is used. 

```
FetchContent_Declare( 
	MyProject
	GIT_REPOSITORY https://github.com/example/repo.git
	GIT_TAG v1.0
	PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/my_patch.patch
)
```
Patch will happen after the update is gotten. 

Then `UPDATE_COMMAND` will apply the update command if the content already exists. 
```
FetchContent_Declare( 
	MyProject
	GIT_REPOSITOYR https://github.com/example/repo.git
	GIT_TAG v1.0
	UPDATE_COMMAND git pull origin main
)
```
Update will do its thing, if the downloader being used has that option. 

```
FetchContent_Declare( 
	GIT_REPOSITORY <url>
	GIT_TAG <tag>
	UPDATE_COMMAND <cmd>
	PATCH_COMMAND <cmd>
)
```


After 3.24 , we can also ask that we look for the files locally before doing any download. 
So just skip the downloading if we can find it locally. 
We just add the `FIND_PACKAGE_ARGS`: 
```
FetchContent_Declare( dependency-id
	GIT_REPOSITORY <url>
	GIT_TAG <tag>
	FIND_PACKAGE_ARGS <args>
)
```

This just means that this a `FetchContent_Declare` uses `find_package()`: 
It should be the last one in the command, and will just pass on all the arguments to the `find_package()`: 

```
cmake_minimum_required(VERSION 3.26)
project(ExternalProject CXX)

add_executable(welcome main.cpp)

configure_file(config.yaml config.yaml COPYONLY) # just copying over file

include(FetchContent)
FetchContent_Declare(external-yaml-cpp
	GIT_REPOSITORY https://github.com/jbeder/yaml-cpp-git
	GIT_TAG        0.8.0
	FIND_PACKAGE_ARGS NAMES yaml-cpp # NAMES is needed here, otherwise external-yaml-cpp will be used
)
FetchContent_MakeAvailable(external-yaml-cpp)
target_link_libraries(welcome PRIVATE yaml-cpp::yaml-cpp)
include(CMakePrintHelpers)
cmake_print_propertiesF(TARGETS yaml-cpp::yaml-cpp
	PROPERTIES TYPE SOURCE_DIR
	INTERFACE_INCLUDE_DIRECTORIES
)
```


[Importing and Exporting Guide](https://cmake.org/cmake/help/latest/guide/importing-exporting/index.html)

Just remember the whole idea of imported targets being that projects outside the CMake and turning them into the logical targets that can be used in CMake. 
They are just made using the `IMPORTED` option of the `add_executable()` and `add_library()` command. 
There are no build files generated. 
Then they can be referenced just like any other target within the project. 

The example here is this, that we use an `IMPORTED` executable target and then references it from the `add_custom_command()` command. 

Running an executable that when run creates a basic `main.cc` in the curr dir. 
The general idea being this: 

```
add_executable(myexe IMPORTED)

set_property(TARGET myexe PROPERTY
	IMPORTED_LOCATION "../InstallMyExe/bin/myexe") # telling cmake the location of the target on disk

add_custom_command(OUTPUT main.cc COMMAND myexe)
```

`OUTPUT` - this custom command will generate an output, `main.cc` will be generated here. Then `main.cc` can be used as a the input to other build steps, just setting up some sort of dependency here. 

Then `COMMAND myexe` will be what is run to generate the output. 

#### ExternalProject
Will populate the dependencies during the build phase, which is different to the configuration phase. 
Meaning it cannot get those targets into the project the way that `FetchContent` does. 
`ExternalProject` can install dependencies directly onto the system, execute their tests, and do other interesting things. 

Probably just read the documentation: 
