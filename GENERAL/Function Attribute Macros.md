Some interesting code. 

[Arch Arm64 Kernel](https://github.com/ARM-software/linux/blob/linux-4.1-mali/arch/arm64/kernel/acpi.c)

One of the main functions here has the macro: `static int __init parse_acpi(char *arg)`
`__init`. 
These are function attribute macros, it is a macro, in this instance they are not keywords. 

### So How Do They Work
In C and definitely in Linux Kernel, we might see the attribute macros `__init` `__cold`. 
`__noreturn`, are used as a form of annotation on functions that have special behaviour. 

Behaviours: 
- Memory Layout
- Optimization Hints
- Section Placement
- Compiler Warnings

They expand to compiler-specific attributes, typically using GCC's `__atttribute__` extension. 

They still get expanded by the pre-processor before the compiler sees the function. 

They are a way to attach metadata to functions, using compiler specific extensions. 

They can influence optimizations, warning generation and linkage behaviour, as mentioned above as well, and section placements. 

Not strictly part of the language, but are part of the compilers, GCC, Clang, MSVC. 

In essence, they change a way the compiler treats a function. 

They are usually added right before or after the function declaration or definition. 

|Attribute|Purpose|
|---|---|
|`noreturn`|Function never returns (useful for optimization and warnings).|
|`deprecated`|Marks a function as deprecated, triggering warnings if used.|
|`always_inline`|Forces the function to be inlined (if possible).|
|`noinline`|Prevents the compiler from inlining the function.|
|`pure` / `const`|Tells the compiler about side-effect-free functions for optimization.|
|`cold`|Marks a function as unlikely to be called (helps with code layout).|
|`malloc`|Indicates the function returns a pointer to newly allocated memory.|
|`format`|Enables `printf`/`scanf`-style format checking.|
|`flatten`|Attempts to inline all calls within the function.|
These are some common ones. 

#### Using Macros with Attribute Macros 
For more portability: 
```
#if defined(__GNUC__) || defined(__clang__)
	#define ATTR_NORETURN __attribute__((noreturn)) // define it to be this if above true
#else   
	#define ATTR_NORETURN         // else define as nothing
#endif

ATTR_NORETURN void fatalError();
``` 

Here we can compile on compilers that don't support the attribute by safely omitting it. h

## C++ 11
Introduced a standard syntax for attributes using double square brackets. 

```
[[noreturn]] void fatalError(); 
[[deprecated("Use newFunc() instead")]] void oldFunc(); 
```
`deprecated` meaning that it's old and should no longer be used, however, it is valid. 
This will help developers move away from old or unsafe APIs. 

