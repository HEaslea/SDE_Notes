Pragma is a compiler specific: 
Pragma is short for pragmatic information: with the next line consisting of information on how a compiler or interpreter or assembler should process the program. 
There are a whole bunch, most of them are compiler specific, and it's worth checking: 

eg. 
### Pragma once
Making sure that a header file is included only once in a single compilation: 
The `ifndef and define` are apparently more efficient, however, I'm not buying it: 
This is supported by all compilers, almost all: 


### Pragma push_macro/pop_macro
`push_macro` pushes the current definition of a macro onto a stack so it can be restored
`pop_macro` pops the last saved macro from the stack, restoring its previous definition: 
Again supported by most compilers: 

```
#pragma push_macro("MAX");
#undef MAX
// redefine or use MAX differently
#pragma pop_macro("MAX");
```

At the end there we are returning `MAX` to what it was beforehand. 

#### Pragma GCC diagnostic
GCC and Clang specific: 
enable and disable specific warnings using this pragma
```
#pragma GCC diagnostic push
#pragma GCC diagnostic ignored "-Wunused-variable"
int unused_variable; // no warning here
#pragma GCC diagnostic pop
```

### Pragma optimize 
MSVC specific: 
```
#pragma optimize("", off)
// code that wont be optimized
#pragma optimize("", on)
```

### Pragma message
GCC, Clang, MSVC
```
#pragma message("Compiling MySturct Definition");
```
providing a message during compilation: 

### Pragma comment
MSVC specific
`#pragma comment(lib, "user32.lib") // Link user32.lib`
