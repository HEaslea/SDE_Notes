These are evaluated at **generate time** (when we CMake builds the build system).
They are for conditional behaviour depending on the build configuration, target properties or other criteria. 
`$< ... >`
They gather information from the generated configuration of targets and the state of the build environment. 

They are providing information specific to each build configuration. 

`target_include_directories(tgt PRIVATE /opt/include/$<CXX_COMPILER_ID>)`

This would expand to `/opt/include/GNU`, `/opt/include/Clang`. 
Remember that the `PRIVATE` part means that only this target's include property will be affect by this. 

```
target_compile_definitions(tgt PRIVATE 
	$<$<VERSION_LESS:$<CXX_COMPILER_VERSION>, 4.2.0>:OLD_COMPILER>
)
```
This will expand to `OLD_COMPILER` if the `CMAKE_CXX_COMPILER_VERSION` is < 4.2.0. 

 `target_compile_definitions(foo PUBLIC BAR=$<TARGET_FILE:baz>)`

For reference with g++: 
`-D` will define preprocessor macros, it's equivalent to writing `#define` in your code. 

Let's say that `g++ -DMY_MACRO source.cpp`

then in our code
```
#ifdef MY_MACRO
	std::cout << "MY_MACRO is defined!" << std::endl;
#endif
// This will output "MY_MACRO is defined!"
```

```
g++ -DVERSION=2 source.cpp
// in code
#ifdef VERSION
	std::cout << "Version: " << VERSION << std::endl;
#endif

// output "Version: 2"
```


```
$<$<STREQUAL:$<TARGET_PROPERTY:MyTarget, PLATFORM_ID>,Linux>:LINUX_BUILD>
```
Here: 
`$<TARGET_PROPERTY:MyTarget, PLATFORM_ID>`
Retrieves the `PLATFORM_ID` property of the target `MyTarget`, which typically holds the platform that that target is being built on. 
`Linux, Windows, Darwin` `Darwin` for `macOS`. 
Then here, we are comparing that retrieved string, to `Linux`. 
If they do match, then we evaluate to `1`, otherwise to `0`. 
Conditional Result: `$<...:LINUX_BUILD>`, the entire expression evaluates to `LINUX_BUILD`. 
As you see that, the `$<STREQUAL:...>` is the second expression in there. 

That's the same as `$<$<VERSION_LESS:$<CXX_COMPILER_VERSION>, 4.2.0>:OLD_COMPILER>`


```
$<$<AND:$<COMPILE_LANGUAGE:CXX>, $<CXX_COMPILER_ID:AppleClang, Clang>>:COMPILING_CXX_WITH_CLANG>
```
This one is super confusing: 
`$<COMPILE_LANGUAGE:CXX>` : Checks if the current compilation unit's language is C++. 
Then `$<CXX_COMPILER_ID:AppleClang, Clang>` : Checking if the compiler ID matches either `AppleClang` or `Clang`, returning 1 if the compiler is one of these and 0 otherwise. 

`$<AND:$<COMPILE_LANGUAGE:CXX>, $<CXX_COMPILER_ID:AppleClang, Clang>>`
And the two conditionals. 

Then the last, outer part is that the expression will expand `COMPILING_CXX_WITH_CLANG`, otherwise, it will evaluate to nothing. 

The more readable version looks something like
```
$<IF:$<AND:$<COMPILE_LANGUAGE:CXX>, $<CXX_COMPILER_ID:AppleClang,Clang>>,COMPILING_CXX_WITH_CLANG,>
```
```
$<$<AND:$<COMPILE_LANGUAGE:CXX>, $<CXX_COMPILER_ID:AppleClang, Clang>>:COMPILING_CXX_WITH_CLANG>
```

`$<IF:$<TARGET_EXISTS:arg>, true_value, false_value>`

```
traget_link_libraries(MyTarget PRIVATE
	$<IF:$<TARGET_EXISTS:Boost::Boost>,Boost::Boost, >
)
```
Check fi the target `Boost::Boost` exists in the current CMake project. 

As we might do something like this: 
`find_package(Boost REQUIRED)` meaning that CMake will search the system in order to find the file. 
If found, then it will make a target called `Boost::Boost` 

### Querying and Transforming
##### Dealing with Strings, Lists, Paths
`$<LOWER_CASE:string>, $<UPPER_CASE:string>`
Lists: 
`$<IN_LIST:string, list>`, true if found in the list. 
`$<JOIN:list, d>` This joins the semicolon separated `list` using a `d` delimiter. 
`$<REMOVE_DUPLICATES:list>`, without sorting. 
`$<FILTER:list,INCLUDE|EXCLUDE,regex>` Includes/excludes items from list using a regex. 


```
# List Operations in CMake

# 1. JOIN: Join list elements into a single string with a separator
$<LIST:JOIN,LIST,SEPARATOR>

# 2. SORT: Sort list elements in lexicographical order
$<LIST:SORT,LIST>

# 3. REMOVE_DUPLICATES: Remove duplicate items from the list
$<LIST:REMOVE_DUPLICATES,LIST>

# 4. REMOVE_ITEM: Remove a specific item from the list
$<LIST:REMOVE_ITEM,LIST,ITEM>

# 5. PREPEND: Add an item to the beginning of the list
$<LIST:PREPEND,LIST,ITEM>

# 6. APPEND: Add an item to the end of the list
$<LIST:APPEND,LIST,ITEM>

# 7. LENGTH: Get the number of elements in the list
$<LIST:LENGTH,LIST>

# 8. INDEX: Find the index of an item in the list
$<LIST:INDEX,LIST,ITEM>

# 9. SUBLIST: Extract a sublist starting from an index with a specified length
$<LIST:SUBLIST,LIST,START_INDEX,LENGTH>

# 10. GET: Get the element at a specific index
$<LIST:GET,LIST,INDEX>

# 11. FIND: Find an item’s index, or -1 if not found
$<LIST:FIND,LIST,ITEM>

# List Operations in CMake

# 1. JOIN: Join list elements into a single string with a separator
$<LIST:JOIN,LIST,SEPARATOR>

# 2. SORT: Sort list elements in lexicographical order
$<LIST:SORT,LIST>

# 3. REMOVE_DUPLICATES: Remove duplicate items from the list
$<LIST:REMOVE_DUPLICATES,LIST>

# 4. REMOVE_ITEM: Remove a specific item from the list
$<LIST:REMOVE_ITEM,LIST,ITEM>

# 5. PREPEND: Add an item to the beginning of the list
$<LIST:PREPEND,LIST,ITEM>

# 6. APPEND: Add an item to the end of the list
$<LIST:APPEND,LIST,ITEM>

# 7. LENGTH: Get the number of elements in the list
$<LIST:LENGTH,LIST>

# 8. INDEX: Find the index of an item in the list
$<LIST:INDEX,LIST,ITEM>

# 9. SUBLIST: Extract a sublist starting from an index with a specified length
$<LIST:SUBLIST,LIST,START_INDEX,LENGTH>

# 10. GET: Get the element at a specific index
$<LIST:GET,LIST,INDEX>

# 11. FIND: Find an item’s index, or -1 if not found
$<LIST:FIND,LIST,ITEM>
```
##### Querying and Transforming Path Names
```
$<PATH:HAS_ROOT_NAME,path>

$<PATH:HAS_ROOT_DIRECTORY,path>

$<PATH:HAS_ROOT_PATH,path>

$<PATH:HAS_FILENAME,path>
```



