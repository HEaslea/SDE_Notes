### Build Systems
Automates the process of compiling, linking and managing dependencies to produce an `exe` or `lib`. 
Determining what needs to be built in what order: 
We need to make a file that they can read off, or just write the right command in and they will do everything that we need. 
`Ninja` - is a build system - and will read off `build.ninja` in order to know what to do. 
`CMake` - will generate these files, based on other files, so on and so forth. 

```
cmake -B build -G "Ninja"
cmake --build build
```


### Toolchains
A set of programs used to transform source code into a final binary. 
1. Compiler (`g++, clang++, cl.exe`) Converting to object files
2. Linker (`ld, link.exe`) Object files into an executable
3. Assembler (`as`) Converts assembly code to machine code. 
4. Debugger (`gdb` `lldb`) duh. 

- GNU `gcc, ld, gdb`
- LLVM `clang, lld, lldb`
- MSVC - `cl.exe, link.exe, msbuild`

### How they Link
Build systems use a tool chain. The automation (build system) that orchestrates the build process, will use the toolchain (the programs that make up the build chain). 

Here is the layout: 
- Toolchain : `g++, ld, ar` (GNU compiler collection)
- Build System : Ninja, Make, MSBuild
- Build System Generator : CMake (creates files for Ninja/Make)
