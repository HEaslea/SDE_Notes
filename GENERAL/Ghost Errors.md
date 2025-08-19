Had this error in writing some code. 

Just happened randomly and took me about an hour to get it going again. 

The code was 100% correct. 

The issue was that we were mixing old `.o``.obj` files, meaning that we were getting some `undefined reference`, even though the code was 100% correct. 

#### Incremental Building
Compilers will only compile files that are changed. 

If a `.cpp` , source file, does not get built, that means that the old `.o`/`.obj` lingers around and will be linked. 

The linker then obviously uses those. 

The source code changing usually updates and will compile that file again, however, if there is no update, despite adding more code to it, then we are going to get an error, and that should be more obvious to see. 

### The Fix
###### Clean Build
We force the build to throw away all the compiled objects and rebuild everything from scratch. 

With just `g++`: 
```
rm -f *.o app.exe               # delete all globbed .o files
g++ main.cpp Manager.cpp -o app # will force all the .o files to be compiled AGAIN
```

In CMake: 
```
cmake --build build --clean-first
```

You could just delete the build dir: 
```
rm -rf build/
cmake -S . -B build
cmake --build build
```
``
If you are ever in doubt, just do this I suppose
