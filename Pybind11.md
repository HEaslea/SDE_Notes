## A General Understanding
Think there is a Python Interpreter on the system - we need to start this (initialize it) and have it run in parallel to the C or C++ code. 
Pybind11 can loosely be though of as a higher-level wrapper around the Python C API. 
`Python.h` is the file that we need to include (through CMake) as that gives us "access" to python objects and types - most importantly a way to embed a python interpreter inside your C++ program. 
The interpreter is the `.exe` that runs Python scripts. 



### `Python.h`
Header file part of the Python C API (A set of functions, routines, and tools that allow one piece of software). 
So this header file provides access to Python's internal functionality from C or C++. 

