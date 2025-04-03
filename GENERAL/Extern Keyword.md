`extern` specifies that the variables that external linkage, ie. <u>in another translation unit</u>. 
[Extern Keyword](https://learn.microsoft.com/en-us/cpp/cpp/extern-cpp?view=msvc-170)
This would have to be put in every file, except the one that defines the variable. 

When a linker sees `extern`, it will look for the definition in another translation unit. 
Declarations of `non_const` variables at global scope are external by default. 
Only apply `extern` to the declarations that don't provide the definition. 
```
// fileA.cpp 
int i = 42; // declaration and definition

// fileB.cpp 
extern int i; // declaration only. same as i in FileA

// fileC.cpp
extern int i; // declaration only again, same as i in FileA

// fileD.cpp 
int i = 43; // LNK2005! 'i' already has as a definition
extern int i = 43; // would still run an error
```

`extern` linkage for `const` globals
```
// fileA.cpp
extern const int i = 42; // extern const definition

// fileB.cpp
extern const int i; //declaration only, fine, as detected as same i, due to no redefinition. 
```


The idea is true of functions too: 

```
extern void externalFunction(); 
```

Once again declaring and not defining. 

Essentially the idea is that there is a definition in another translation unit and we are using that one. More so for linkage, and not so much for include. 