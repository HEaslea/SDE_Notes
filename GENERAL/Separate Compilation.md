Complicated programs will require parts of the program to be stored in separate files. 
Eg. storing the functions used in one file and the code that uses the functions in other source files. 
To accompany this, separate compilation is a thing. 
It lets us split our programs into several files, each of which can be compiled independently. 

#### Compiling and Linking Multiple Source Files
Assume the definition of our `fact` function is in a file named, `fact.cc` and its declaration is in a header file named `Chapter6.h`. 
Our fact file will include the header ofc. 
We'll store a main function that calls fact in a second file named `factMain.cc`. 
To produce an exe file, we must tell the compiler where to find all the code we use. 

We might compile these files as follows: 
```
$ CC factMain.cc fact.cc # generates factMain.exe or a.out
$ CC factMain.cc fact.cc -o main # generates main or main.exe
```

CC is the name of our compiler, $ is our system prompt, and # begins a command line (like in python). 
We can now run the exe file, which will run our `main` function. 

Most compilers give us a way to compile separately. 

This process usually yields a file with the `.obj` (Windows) or `.o` (UNIX), indicating that the file contains object code (machine code, not yet linked into a complete program). 

The compiler allows us to link object files together to form an exe. 
On the system we use, we would separately compile our program as follows: 
```
$ CC -c factmain.cc # generates factMain.o
$ CC -c fact.cc # generates fact.o
$ CC -c factMain.o fact.o # generates factMain.exe or a.out
$ CC -c factMain.o fact.o -o main # generates main or main.exe
```
Check with compiler user guide in order to understand how to compile and execute programs made up of multiple source files. 

