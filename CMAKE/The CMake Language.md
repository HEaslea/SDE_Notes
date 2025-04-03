Like all other languages, we go from top to bottom, occasionally we jump to an included fileor a called function. 
Starting point of execution is determined by the `mode`. 
Either from the root file or the source tree (`CMakeLists.txt`). or a script file that is provided as an argument to cmake. 
Starting with some simple listfiles, with scripts so that we can just get used to what we are looking at. 

`cmake -P script.cmake`
Everything in a `CMake` is either a comment or command invocation. 

### Comments
I guess we are starting here: 
There are single line and multiline
`#` for single line. 
Multi-line is called bracket comments
They start with `#` and followed by `[` and any number of equal signs (which can also be 0), and another square bracket. then to close, we use the same number of equal signs and reverse the brackets `]`. 
```
#[=[
	bracket comment
	#[[
		neseted bracket comment
	]]#
]=]#
```
You can deactivate a multiline comment swiftly, by adding another # to the initial line of the bracket comment
```
##[=[
	no longer commented
		#[[
			still, a nested comment
		]]#
#]=] this is a single line comment now
```
#### Command Invocations
In order to make a command, write its name, followed by parentheses, in which you enclose a list of **command arguments** separated by whitespace. 
`message("hello" world)`
Command names are not case sensitive. 
There is the convention that we use snake case in the CMake community. 
You cannot pass another command, everything inside the parentheses is treated as an argument for that specific command. 
They are not followed by semicolons. 
As each line of source code can only contain one command: 
```
command(argument1 "argument2" argumen3) # comment
command2() #[[ multiline comment
```

**Scripting Commands:** - Always available and they change the state of the command processor, and access variables, affect other commands and the environment. 
**Project Commands**: - These are available in projects and they manipulate the project state and the build targets. 

### Command Arguments
White space separated arguments to configure their behaviour. 
Some arguments require `"` and some don't. 
Under the hood, the only data type recognized by CMake is the `string`. 
Every command expects zero or more strings for its arguments. 
CMake will evaluate every argument to `string`. Evaluating means **string interpolation**, or substituting parts of a string with another value. 
This can mean replacing the escape sequences, expanding references (also called variable interpolation) and unpacking lists. 

`\n` : newline
`\t` : tab 
`\\` : literal backlash
`\"` : double quote

```
message("Line 1\nLine 2") # Outputs as : 
#Line 1
#Line 2
```
`set(PATH "C:\\Program Files\\MyApp") # escaping backslashes for Windows paths`. 

###### Expanding References in CMake

```
set(VAR "Hello")
message("${VAR} World") # Outputs: Hello World
```

```
set(FOO "foo")
set(foo_var "Value of foo")

# construct the varible name and expand it
set(VARNAME "${FOO}_var")
message("${${VARNAME}}") # Outputs : Value of foo
```

###### String Interpolation
This is similar to variable substitution or expanding references, where CMake automatically replaces variable names within strings with their values. 
`${}` to put variables directly into strings. 
String interpolation in other languages looks something like this: 
`var text = $"City: {city}, area: {area}";
###### Dynamic Variable Construction
```
set(VAR1 "Hello")
set(VAR2 "World")

# create a new variable name by combining other variables
set(PREFIX "VAR")
set(INDEX 1)
set(DYNAMIC_VAR_NAME "${PREFIX}${INDEX})

# Access the dynamically constructed variable name
message("${${DYNAMIC_VAR_NAME}} ${VAR2}") # Outputs : Hello World
```
#### Bracket Arguments
They are used to pass multiline strings verbatim, as single argument to commands. 
They are identical to comments. They are initiated with `[=[` and concluded with `]=]`. 
The number of `=` must be the same of course. 
The difference being that we cannot nest them in these situations. 

```
message([[multiline
	bracket
	argument
]])

message([==[
	because we used two equal-signs "=="
	this command receives only a single argument
	even if it includes two square brackets in a row
	{ "petsArray" = [["mouse", "cat"], ["dog"]]}
]==])
```
This will output to this: 
```
multiline
bracket
argument
	because we used two equal-signs "=="
	following is still a single argument: 
	{ "petsArray" = [["mouse", "cat"], ["dog"]]}
```
The following is super useful when we pass text that contains double brackets (`]]`) as they won't be interpreted as marking the end of the argument. 

#### Quoted Arguments
The quotes will extend arguments to include whitespace. 
Where we can use escape characters as well, as above. 
They can span over lines and use variables references. 
```
message("1. escape sequence: \" \n in a quoted argument")
message("2. multi....
line")
message("3. and a variable reference ${CMAKE_VERSION}")
```
We are using the `CMAKE_VERSION` available here. 
Preserving white spaces and special character within the quotes as part of the string. 
Treated as a single argument. 
```
set(MY_VAR "Hello World") # a single string with spaces
message("${MY_VAR}") # output : Hello World

set(MY_PATH "C:\\Program Files\\MyApp") # escaping backslashes
message("${MY_PATH}")
```
### Unquoted Arguments
They both evaluate both escape sequences and variable references. 
Semicolons are used as list delimiters. 
Without quotes, CMake will see these as a sequence of words, which will lead to multiple arguments. 
Semicolons are used as list delimiters. 
If an argument contains a semicolon, it will be split into multiple arguments. 
We can escape the semi colon `\;`. 
We can also escape white space `\ `. 

`argum\ ent\;1`
This is just one argument, everything is escaped nicely etc. 

`arg;ume nts`
This is 3 arguments, one before `arg`, one after `ume`, and the last after non escaped white space `nts`. 

```
message(a\ single\ argument)
message(two arguments)
message(three;separated;arguments)
message(${CMAKE_VERSION}) # variable referencing
message(()()()) # matching parentheses
```

Which will output as: 
```
a single argument
twoarguments
threeseparatedarguments
3.16.3
()()()
```
Remember, the most important thing here is that every single argument is a string. Then commands will act upon those strings. 
Unmatched parentheses are not allowed, however, matching. 
Message will not print the spaces, it will just stick all the arguments together, given `twoarguments`. 

There are optional arguments too: 
`project(myProject VERSION 1.2.3)`
`VERSION` and `1.2.3` these are both optional. 

#### Variables

##### Normal Variables
Defined within a CMake script, only available in the current scope unless explicitly passed to a subdirectory or function. 
Persist through the script but do not propagate automatically to other scopes (eg. parent/child directories or functions). 
```
set(MY_VAR "Hello, Cmake") # Remember that quoted args are just one argument
message(${MY_VAR})# output : Hello, Cmake
```


##### Cache Variables
Persistent across multiple runs of `cmake` commands. Stored in the `CMakeCache.txt`. 
Retained even after re-runs of `cmake`, unless explicitly cleared. 
Used to configure build options, that users might want to modify eg. enabling or disabling build features. 
```
set(VARIABLE_NAME value CACHE TYPE "Description")
```
Cache types can be: 
- BOOL
- PATH : File system paths
- FILEPATH : path to a file
- STRING : any string value
- INTERNAL : used internally by cmake

```
set(MY_CACHE_VAR "default" CACHE STRING "a cached variable")
message(${MY_CACHE_VAR})
```


##### Environment Variables
Accessed from the system's environment. 
Available in all scopes if exported from the shell or inherited by the process running `cmake`. 
Retrieving extern configuration like paths, tools, or settings. 
`ENV{VAR_NAME}`

```
message($ENV{HOME}) # outputs the user's home directory
```

##### Global Variables
Accessible across all directories processed in a single CMake project. 
Commonly set via commands like `project()` `add_library()`
Predefined like `CMAKE_SOURCE_DIR`, `CMAKE_BINARY_DIR`. 

```
message(${CMAKE_SOURCE_DIR}) # outputs the root source directory
message(${CMAKE_BINARY_DIR}) # outputs the root binary directory
```
The standard scope set for variables, unless explicitly set otherwise: 
`set(MY_GLOBAL_VAR "Some Value")`
This will be accessible anywhere in the script that we are in. 
Then we can set again other that: 
`set (MY_GLOBAL_VAR "New Value")`

##### Built-in Cmake Variables
Predefined by `cmake` and often related to the system or build environment. 
Automatically populated with information about the compiler, platform, or project. 
```
CMAKE_CXX_COMPILER : path to the C++ compiler 
CMAKE_BUILD_TYPE : Build configuration (Debug, release)
CMAKE_INSTALL_PREFIX : default installation path 
```
```
message(${CMAKE_CXX_COMPILER})
```

##### Variables in Functions (Global Scope)
Any variables that are defined inside functions will be local to that function. 
In order to keep them global, we need to say, and mark them with `CACHE` or modify them int he global scope using `set()` with the `PARENT_SCOPE`  option: 
```
function(set_global_variable)
	set(MY_GLOBAL_VAR "Modified Inside Function, but still global", PARENT_SCOPE)
endfunction()

# calling the function
set_global_variable()

message("MY GLOBAL VAR IS: ${MY_GLOBAL_VAR})
```

#### Variables in Different Scope
OUTER SCOPE VARIABLES ARE COPIED INTO THE INNER SCOPE. 
MODIFICATIONS TO VARIABLES IN THE INNER SCOPE WILL NOT AFFECT THOSE IN THE OUTER SCOPE, UNLESS WE SPECIFY THAT. 

Variables that are defined at the top level of the CMake script (remember what top level statements are during C#). These are made in the GLOBAL SCOPE, meaning that they are accessible throughout the entire script.

Variables that are defined in a function or a macro, ARE LOCAL TO THAT SCOPE. When passing vars in a function, the variables are copied into the scope. 
Any changes made to these will only affect the local scope, unless explicitly told to modify the outer global scope. 

```
set(OUTER_VAR "Initial Var")

# Function that uses OUTER_VAR
function(change_variable)
	set(OUTER_VAR "modified inside function") # this modifies ONLY local scope
	message("Inside Function, OUTER_VAR ${OUTER_VAR}")
endfunction()

message("Before function call, OUTER_VAR: ${OUTER_VAR}")

# call function
change_varible()

message("After function call : OUTER_VAR : ${OUTER_VAR}")
```


##### Using `PARENT_SCOPE` To Modify Outer Variables

This goes in teh `set()`, in order to access the outer variables: 
```
function(change_variable)
	set(OUTER_VAR "Modified Inside Function!" PARENT_SCOPE) # accessing the outer scope
endfunction()
```

> Remember that conditional blocks, loop blocks, and macros don't create separate scopes

#### `block()`
Used to create groupings of CMake commands that will be executed together. 
Good for organizing and structuring the CMake code. 
```
block(<name> <commands...>)
endblock()
```

[Block from Docs](https://cmake.org/cmake/help/latest/command/block.html)

it will create a new scope: 
```
block([SCOPE_FOR [POLICIES] [VARIABLES]] [PROPAGATE <var-name>...])
  <commands>
endblock()
```
```
block(SCOPE_FOR POLICIES CMP0074 VARIABLES VAR ANOTHER_VAR PROPAGATE VAR)
```
`PROPAGATE` - Allow to specify which vars will be **propagated** (made available), to the outer scope when the block ends. Allowing some vars to persist after the var. 
It will be set in the parent scope if we do propagate something. 

Another thing worth remembering is that when we use the `PARENT_SCOPE`, it won't change the vars that we have in the current scope. 


#### Using Lists
We use `;` as the delimiter. 
They are all concatenated into a long string, with the `;` delimiter that we see: 
```
a;list;of;5;elements

a\;single\;element
```
Lists can have 0 or more elements; 0 being important I imagine. 

```
set(myList item1 item2 item3)
will implicitly become "item1;item2;item3"
set(myList "a;list;of;five;elements") # the same
set(my List a list "of;five;elements") # the same
```

Accessing: 
```
set(myList item1 item2 item3)
list(GET myList 1 var_name)
message("The second item is: ${var_name}") # outputs "item2"
```
Appending: 
```
list(APPEND myList item4 item5)
# myListy is now "item1;item2;item3;item4;item5"
```
Length of a List: 
```
list(LENGTH myList length_var)
message("List Lenght: ${length_var}") # outputs 5
```
Inserting Items: 
```
list(INSERT myList 1 new_item)
#myList becomes "item1;new_item;item2;item3;item4;item5"
```
Removing items: 
```
list(REMOVE_ITEM myList item3)
# myList becomes "item1;new_item;item2;item4;item5"

list(REMOVE_AT my_list 2)
# myList becomes "item1;new_item;item4;item5"
```
Splitting a String into a List: 
```
stirng(REPLACE "," ";" myList "a,b,c,d")
#myList is now "a;b;c;d"
```
Joining a List: 
```
list(JOIN myList ", " joined_string)
message("JOINED: ${joined_string}") 
# outputs "item1, new_item, item4, item5"
```


```
set(SOURCES main.cpp utils.cpp)
list(APPEND SOURCES debug.cpp)
message("Sources ${SOURCES}") #Outputs "main.cpp;utils.cpp;debug.cpp"
```


```
set(COMPILER_FLAGS -Wall -Wextra)
list(APPEND COMPILER_FLAGS -O2)
message("Compiler Flags: ${COMPILER_FLAGS}") # Ouputs "-Wall;-Wextra;-O2"
```
They are not mutable in place, modifying them creates a new string representation of the list: 

```
message("The List is: " ${myList})
```
Here `message` will be given the unpacked `myList`, which will give us the uninteded: 
`The List is: alistoffiveelements`. 

```
list(LENGTH <list> <out var>)
list(GET <list> <element index> [<index> ...] <out var>)
list(JOIN <list> <glue> <out var>)
list(SUBLIST <list> <begin> <length> <out var>)
list(FIND <list> [<element> ...])
list(APPEND <list> [<element> ...])
list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
list(ISNERT <list> <index> [<element> ...])
list(POP_BACK <list> [<out var> ...])
list(POP_FRONT <list> [<out var> ...])
list(PREPEND <list> [<out var> ...])
list(REMOVE_AT <list> <index>...)
list(REMOVE_DUPLICATES <list>)
list(TRANSFORM <list> <ACTION> [...])
list(SORT <list> [...])
```

### Control Structures
**Conditional Blocks, Loops and Command Definitions**
##### Conditional Blocks
```
if(<condition>)
	<commands> 
elseif(<condition>)
	<commands>
else()
	<commands>
```

```
set(MY_VAR ON)

if(MY_VAR)
	message("MY_VAR is ON")
else() 
	message("MY_VAR is off")
```


NO LOCAL VARIABLE SCOPE IS CREATED IN ANY OF THE CONDITIONAL BLOCKS. 

##### Logical Operators
`NOT, AND, OR`
- `NOT <condition>`
- `<condition> AND <condition>`
- `<condition> OR <condition>`

Nesting of these are possible, as long as we use parentheses: 
```
(<condition>) AND (<condition> OR (<condition>))
```
##### The Evaluation Of a String and a Variable
This means, that in `if` statements, we don't have to var ref: 
It will try and evaluate unquoted arguments as if they are variable references.
```
set(BAX FALSE)
set(QUX "BAZ")
if(${QUX}})
```

This means that in the `if`, it will be evaluated to BAZ, which in turn is evaluated to FALSE. 
Strings are only considered boolean true if they evaluate/equal (case sensitive): `ON, Y, YES, TRUE` or a non zero number

```
set(FOO BAR)
if(FOO)
```

WEIRDLY, this will not evaluate to false, like we said before. 
CMake will make an exception for unquoted variable references. 
Unlike quoted arguments, `FOO` won't be evaluated to BAR to produce an `if("BAR")` statement, which would be false. 
Here, it will only evaluate this to false if: 
- `OFF, NO, FALSE, N, IGNORE, NOTFOUND`
- A string ending with `-NOTFOUND`
- Any empty string
- Zero

If on an undefined variable will evaluate to false: 
`if(CORGE)`, if this is undefined var, then it will be false

If we set that value: 
```
set(CORGE "A Value")
if(CORGE)
```
This will be evaluated to true. 

Here the `if` is just checking that it is defined or not. 

```
if(DEFINED <name>)
if(DEFINED CACHE{<name>})
if(DEFINED ENV{<name>})
```

##### Comparing Values
`EQUAL, LESS, LESS_EQUAL, GREATER, GREATER_EQUAL`. 

```
set(VALUE 10)
if(VALUE EQUAL 10)
	message("VALUE IS 10")
endif()
```

Remember this `major[.minor[.patch[.tweak]]]`
We can do something like this: 

**String Comparisons**

```
set(STRING1 "Hello")
set(STRING2 "World")

if(STRING1 STREQUAL "Hello")
if(NOT STRING1 STREQUAL STRING2)
```

**Checking Variable Existence**
```
if(DEFINEdD SOME_VARIABLE) # (big if) True if the var exists
```

**Boolean Comparisons**: 
Cmake will treat vars as boolean if they are `ON, OFF, TRUE, FALSE, YES, NO, 1, 0`. 

```
set(IS_ENABLED ON)

if(IS_ENABLED)

if(NOT IS_ENABLED)
```

**Combining Combinations**
```
set(VALUE 10)
set(STRING "CMake")

if(VALUE GREATER 5 AND STRING STREQUAL "CMake")
```

**Advanced String Matching**
```
set(STRING "Hello123")
if(STRING MATCHES "^[A-Za-z]+[0-9]+$")
```
This is POSIX Regex!

**Lists and String Containment**
```
set(MY_LIST "item1;item2;item3")
if("item2" IN_LIST MY_LIST)

set(MY_STRING "Hello CMake")
if(MY_STRING MATCHES "CMake")
```

**Version Compare**
if(1.3.4 VERSION_LESS_EQUAL 1.4)

#### Explaining the Filesystem

Checking if a file or directory exists: 
`EXISTS <path-to-file-or-directory>: `
This will return true if the file or dir exists. 
`<file1> IS_NEWER_THAN <file2>` will return true if `file1` is newer. 

`IS_DIRECTORY` will check if the path is to a dir or not. 
`IS_SYMLINK` will check if the path is a symbolic link or not. 

`IS_ABSOLUTE` will check fi the path is absolute. 

`if("/a////b/c" PATH_EQUAL "/a/b/c/") # returns true`

### Loops
There is `while()` and `foreach()`. 
- `break()`
- `continue()`

There is no local variable scope created in either of these. 
Don't forget `enwhile()` and `endforeach()`. 

```
foreach(<loop var> RANGE <max>)
endforeach()
```

This will loop over from 0 to `<max>` and be inclusive with. 
We can also be more specific: 
```
foreach(<loop_var> RANGE <min> <max> [<step>])
```
These all have to be non-negative. 

With lists is the main thing: 
`foeach(<loop var> in [LISTS <lists>] [ITEMS <items>])`

```
foreach(loop_var IN LISTS list_var)
	# execute for each item in the list
endforeach()
```

```
foreach(loop_var IN ITEMS item1 item2 item3 ...)
endforeach()
foreach(fruit IN ITEMS apple banana cherry)
endforeach()
```

Then combining them: 
```
foreach(loop_var IN ITEMS item1 item2 ... IN LISTS list_var1 list_var2 ... )
endforeach()
```

```
set(my_list "grape" "melon")
set(other_list "kiwi" "peach")

foreach(fruit IN ITEMS apple banana IN LISTS my_list IN ITEMS cherry IN LISTS other_list)
  message("Fruit: ${fruit}")
endforeach()


#output 
Fruit: apple
Fruit: banana
Fruit: grape
Fruit: melon
Fruit: cherry
Fruit: kiwi
Fruit: peach
```
They just combine, nothing really special happens at all. 

There is a shorter version: 
```
foreach(VAR 1 2 3 4 e f)
endforeach() # which works basically the same way
```

There is also the idea of zipping two lists together: 
```
foreach(<loop_var> in ZIP_LISTS <lists>)
```

```
set(L1 "one;two;three;four")
set(L2 "1;2;3;4;5")

foreach(num IN ZIP_LISTS L1 L2)
	message("Word = ${num_0}, num = ${num_1}")
endforeach()
```

CMake here will make a `num_<N>` variable for each list provided, which it will fill with items from each list. 
So that `num_0` will be for the first list and `num_1` will be for the second list

```
set(LIST1 "one;two;three;four")

set(LIST2 "1;2;3;4;5")

SET(LIST3 "A;B;C;D;E")

  

foreach(num IN ZIP_LISTS LIST1 LIST2 LIST3)

    message("WORD = ${num_0} NUM = ${num_1}, LETTER = ${num_2}")

endforeach()
```

![[Pasted image 20241121020040.png]]

```
foreach(num word letter IN ZIP_LISTS LIST1 LIST2 LIST3)

    message("num = ${num} word = ${word} letter = ${letter}")

endforeach()
```
This works the same way and may be a little easier to use. 


I believe there are scoped variables in the foreach thing. 

#### Command Definitions
`macro()`, which are like C-style macros,  or `function()`, which are closest to C++ functions commands. 
`macro()` is a find and replace deal. 
`macro()` will not put something on the call stack, therefore, do not put a `return()` in these. 

`function()` will also create a local scope for our variables, unlike `macro()`  that will work in the variable scope of the caller. 

In addition we also have access to these: 
```
${ARGC} : The count of arguments
${ARGV} : all arguments as a list
${ARGV<index>} : give the value of an argument specified at index (starting from 0), regardless of whether it was expected or not. 
${ARGN} : A list of anonymous arguments that were passed by the caller after the last expected argument. 
```

##### Macro
```
macro(<name> [<argument>...])
	<commands>
endmacro()
```

Remember that macros will not create a separate frame on the call stack or a new variable scop . 

```
macro(MyMacro myVar)
	set(myVar "new value")
endmacro()

set(myVar "First value")
message("MyVar is now : ${myVar}")
MyMacro("Called value")
message("myVar is now: ${myVar}")
```
```
myVar is now: first value
argument: called value
myVar is now: new value
```

The reason for this weird behaviour is that arguments passed to macros aren't treated as real variables but rather, as constant find and replace instructions. 

Remember this key thing, that macros will not evaluate arguments the same way that functions will. 

When we pass `MyMacro("Called Value")` the literal string "Called Value" is assigned to the macro argument `myVar`. It's not treated as variable substitution. 
Remember, that macros do not create a new scope. 
Then the `set` command inside the macro overwrites `myVar` in the calling scope. 

The argument name `myVar` temporarily shadows the parent `myVar` variable for the duration of the macro. 
This means: inside the macro `myVar` holds the passed value (`"called value"`). 
When `set(myVar "new value")` is called, it modifies the parent `myVar` variable directly because they have no scope isolation. 
Macros here are textual substitutions and don't create a function like scope. 
This is why functions are preferred over macros. 

Remember in C: 
`#define SQUARE(x) (x * x)`
This will just replace anything, literally: 
`int a = SQUARE(5)`
Apparently macros are very similar to template, that allow argument substitution while working in the parent scope. 

Think of our macro as a template: 
Whenever we call `myMacro("called value")` we are saying that for `myVar` in the macro, replace it with `"caled value"`, however, this is not a pure text replacement, as CMake will understand things like `set` and `message` and resolves them in the parent scope. 
This is kind of jank here. 

The `set(myVar "new value")` will look for `myVar` in the parent scope. 
The find and replace idea is just within the macro itself. 
When we define a macro, CMake will find and replace the arguments within the body of the macro. 
This means that nay time `myVar` appears in the macro body, it will get replaced with the value passed to the macro. 
Commands such as `set()` or `messsage()` will still execute inthe parent scope. 

```
macro(conditionalMessage myCondition)
	if(${myCondition})
		message("Condition is True")
	else()
		message("Condition is False")
	endif()
endmacro()

set(myCondition TRUE)
conditionalMessage(myCondition)

set(myCondition FALSE)
conditionalMesage(myCondition)
```
```
macro(concatenateStrings str1 str2)
	set(result "${str1} ${str2}")
	message("Concatenated Result: ${result})
endmacro()

concatenateString("Hello" "World")
message("Result in global ${result}") # will output the result here
# is there is no scope for these
```

```
macro(printListItems myList)
	foreach(item IN LISTS myList)
		message("List Item: ${item}")
	endforeach()
endmacro()

set(myList "apple" "banana" "cherry")
message("Before Macro Call:")
printListItems(myList)
```

```
macro(copyFile src dst)
	file(COPY ${src} DESTINATION ${dst})
	message("Copied File from ${src} to ${dst}")
endmacro()
```

The idea being that we are going to find and replace everything within the macro with the variable that we have passed in. 
The body of the macro is sort of a lie, only  the find and replace within the "body". 


## Functions
```
fucntion(<name> [<argument>...])
	<commands>
endfunction()
```
Here we will have our own variable scope. 
Any `set()` will be done in the local scope, not the parent this time. 
Unless `PARENT_SCOPE` is used. 
The `function()` will be referring to the regular call stack, and we can `return()` to the caller. 
It will allow a `PROPAGATE` with a list of variable names. 
This will return the value from the local scope back to the caller. 

CMake sets the following variables for each function: 
- `CMAKE_CURRENT_FUNCTION`
- `CMAKE_CURRENT_FUNCTION_LIST_DIR`
- `CMAKE_CURRENT_FUNCTION_LIST_FILE`
- `CMAKE_CURRENT_FUNCTION_LIST_LINE`

```
function(MyFunction FirstArg)
	message("Function: ${CMAKE_CURRENT_FUNCTION}")
	message("File: ${CMAKE_CURRENT_FUNCTION_LIST_FILE}")
	message("First Arg: )
	set(FirstArg "new value")
	message("FirstArg againt: ${FirstArg}")
	message("ARV0: ${ARGV0} ARGV1: ${ARGV1} ARCG: ${ARGC}")
endfunction()

set(FirstArg "first value")
myFunction("Value1" "Value2")
message("FirstArge in Global Scope: ${FirstArg}")
```

```
Function: MyFunction
File: /root ..... etc. you get the idea
FirstArg: Value1
FirstArg again : new value
ARGV0: Value1 ARGV1: Value2 ARGC: 2
FirstArt in global scope: first value
```

There will be fewer errors, these are much more understandable. 
The idea is that we are copying as we go in as well. 

#### Procedural Paradigm
Imagine that we have our `CMakeLists.txt` listfile that will call three defined commands that may call defined commands of their own. 

In CMake we have to define commands before we can call them. 

The way to write the definitions after all the main code is with a macro, so that we don't have all the definitions at the beginning, however, that is mostly fine. 
```
macro(main)
	first_step()
	second_step()
	thrid_step()
endmacro()

function(first_step)
function(second_step)
function(third_step)
```

## Frequently Used
##### `message()`
`message(<MODE> "text to print")`
There are quite a few registered MODEs. 
[Documentation](https://cmake.org/cmake/help/latest/command/message.html)

There is also the idea of `CMAKE_MESSAGE_CONTENT`
```
function(foo)
	list(APPEND CMAKE_MESSAGE_CONTEXT "In Foo Function")
	message("Foo Message")
endfunction()


list(APPEND CMAKE_MESSAGE_CONTEXT "Top")
message("Before Function")
foo()
message("After Function")
```

Run with: `cmake -P <file> --log-context`
![[Pasted image 20241121044545.png]]

You get the idea!
We can also use indent here to make this work really well. 

```
list(APPEND CMAKE_MESSAGE_INDENT "  ")
message("Before foo")
foo() 
message("After foo")
```

You don't have a good logger in CMake, therefore you really have to use the logger here. 
The indent thing is super useful inside functions in my opinion. 
In functions you can just start with: 
`list(APPEND CMAKE_MESSAGE_INDENT "In FOO():    "`

##### `include()`
Loads and runs CMake code from the file given.

Partitioning code into other files, keeps things ordered. 
`include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>] [NO_POLICY_SCOPE])`
If a module is specified instead of a file, the file with the name `<modulename>.cmake` is searched first in `CMAKE_MODULE_PATH`, then in the CMake module directory. 
If `RESULT_VARIABLE` is given the variable `<var>` will be set to the full filename which has been included or `NOTFOUND` if it failed. 

The main thing about this is that any code run will not run in a nested variable scope, it will be run in the same scope as the caller. 

If we set `OPTIONAL` then we will not get an error if the file is found. 
Therefore, we can use the `RESULT_VARIABLE`. 

`CMAKE_CURRENT_LIST_DIR` is a great thing. 

##### `include_guard()`
When we include files that have side effects. Then we have to make sure that we `include_guard()` so that we don't include more than once. 
You will have to put that at the start of the included file. 
The first time, CMake will make a note, then not include it again. 
`include_guard([DIRECTORY|GLOBAL])`
The directory thing will just do it for the directory, global for the whole build. 
This works the same as header files, where we put the include guard in the file itself. 
However, there is the idea that we actually have to open the file, and that causes some issues, however, computers, they are pretty quick lol. 

#### `file()`
Possibly the most crazy command of them all. 
```
file(READ <filename> <out_var> [...])
file({WRITE | APPEND} <filename> <content> ...)
file(DOWNLOAD <url> [<file>] [...])
```

```
# Write to a file (overwrite or create)
file(WRITE <filename> <content>)

# Append to a file (create if it doesn’t exist)
file(APPEND <filename> <content>)

# Read a file’s content into a variable
file(READ <filename> <variable>)

# Copy files or directories
file(COPY <source> DESTINATION <destination>)

# Remove files
file(REMOVE <file1> <file2> ...)

# Rename or move a file
file(RENAME <oldname> <newname>)

# Create a directory (including parent directories)
file(MAKE_DIRECTORY <directory>)

# Collect files matching a pattern
file(GLOB <variable> <glob_expr>)

# Collect files recursively matching a pattern
file(GLOB_RECURSE <variable> <glob_expr>)

# Convert to a CMake-style path
file(TO_CMAKE_PATH <native_path> <output_variable>)

# Convert to a native-style path
file(TO_NATIVE_PATH <cmake_path> <output_variable>)

```



#### The Build Folder
The output folder for all the files necessary for building your project are generated. 
The files in there will define how the project is built. 
`Makefile` - Containing rules and dependencies for building our project. 

`build.ninja`  - If using Ninja as a generator, similar to MakeFile, but for ninja build tool. 

**CMake Metadata**: 
`CMakeCache.txt` - stores CMake's configurations settings, including paths, compiler details, and variable values. This can be edited manually or **deleted in order to force a reconfiguration**. 
`CMakeFiles` - A common directory containing internal files used by CMake during the Build Process. 

**Compile Artifacts**: object files `.o` `.obj`. Executables `.exe` `.out` Libraries `.a` `.lib` `.so` `.dll`. 

**Temporary Files**. 




## Targets
Representing executables, libraries, and utilities built by Make. 
`add_library` `add_executable` `add_custom_target`, these will create a target. 
This will create a target named foo, that is a static library, with `foo1.c` `foo2.c`
`add_library(foo STATIC foo1.c foo2.c)`. 

`foo` is now available everywhere else. 
CMake will expand the name into the library when needed. 
Libs can be `STATIC, SHARED, MODULE`. 
`MODULE` indicating that the lib must be dynamically loaded into an executable. 

Executables are more straight forward, `WIN32`, would just add the idea that we need a `WinMain Entry` point. 

Targets also keep track of general properties. 
These can be set and retrieved using the `set_target_properties` and `get_target_property`. 

Targets will store a list of files that they have to link against, which are set using `target_link_libraries`, these can be the name of libraries, or the paths etc. 

Targets can also contain the custom commands that will be run after building.

