#compilationrunthrough
[Video of Reference](https://www.youtube.com/watch?v=ksJ9bdSX5Yo)

The basic process: 

> `source.cpp` -> essentially a text file

> Preprocessor stage -> used for things that start with `#`. Takes source into `source.i`

> Compiler -> generate intermediate representation of your code, and assembly code.

> Assembler -> ASM taking in source, generating machine code. OBJ or BINARY file created. 
> Linker -> bringing in other libraries, other `.o` files. 

There are static and dynamic linkers. 