#aidsfordebugging
The idea being that program will contain debugging code, that is executed only while the program is being developed. Code that is executed only while being developed. 

Obviously, when shipped, the code is turned off, using two pre-processor facilities; `assert` and `NDEBUG`.

### `assert`
#assert
This is a pre-processor macro. 
Acts somewhat like an `inline` function; 
`assert(expr);`

Evaluates the expr, if false (ie. zero) then `assert` writes a message and terminates the program. If the expression is True, is nonzero, then `assert` does nothing. 
`assert` is defined in the `cassert` header. 

`assert` is often used to check for conditions that "cannot happen". 

A program for input of text, knowing that all words it is given are always longer than a threshold, this might contain something like this: 
`assert(word.size() > threshold);`

### The `NDEBUG` Pre-processor Variable
`assert` depends on the status of a pre-processor variable named `NDEBUG`. If `NDEBUG` is defined, assert does nothing. 
This might be a little confusing at this very moment. 

If `NDEBUG` is <u>not</u> defined, `assert` performs RUNTIME checks. 

We can "turn off" debugging by providing a `#define` to define `NDEBUG`. 

Alternatively, most compilers provide a command-line option that lets us define pre-processor variables: 
`$ CC -D NDEBUG main.C # use /D with the Microsoft Compiler`. 
This will have the same effect as writing `#define NDEBUG` at the beginning of `main.C`. 
When NDEBUG is defined, then we avoid the potential run-time overhead involved in checking various conditions. 

`assert` should therefore only be used to verify things that truly should not be possible. 
Useful as an aid in getting a program debugged but should not be used to substitute for run-time logic checks or error checking that the program should do. 

In addition to this, `assert` can write our own conditional debugging code using `NDEBUG`. 
IF `NDEBUG` is <u>not</u> defined, the code between `#ifndef` and `#endif` is executed. 
If `NDEBUG` is defined, that code is ignored. 

SO BASICALLY; not debug, if it is defined, then we ignore the code. If it is not defined, if DEBUG is defined in essence, then we do all the code. 

```
void print(const int ia[], size_t size)
{ 
#ifndef NDEBUG
}
```
I literally have no idea what I'm looking at here. 


