From the core: 
1. Function Code : Compiled to Machine Code and stored in program's code segment (text segment) of memory. This is typically Read only. 
2. Function Address: is used to invoke the function, this address points to the starting location of the function's machine code.

## Function Pointers
```
void(*functionTakingInInt)(int)

void myFunction(int x)
{}

int main() { 
	// or auto functionPointer = myFunction;
	functionTakingInInt = myFunction;
	functionTakingInInt(10);
}
```

## Calling a Function
The function call is translated into assembly instructions that perform a series of operations to execute the function. 

Arguments for that function are pushed onto the stack (or put in registers, depending on the calling convention). 
Then we jump to the memory address where the functions' code beings. This is obtained from the function pointer. 
The return address is saved, making recursion possible, that is saved on the call stack. 
When finished executing, we return to the return address. 

## Calling Conventions
Define how functions receive parameters and return values: 
- Parameter passing : whether params are passed in registers or on the stack;
- Stack Management : who is responsible for cleaning up the stack (caller or the called)
- Return Values : how return values are passed back to the caller (usually in registers)
![[Pasted image 20240827031223.png]]

# Name Mangling
When we have function overloading: 
```
void print(int x);
void print(float x);
```

Then we might have name mangling as well: where the compiler might translate `print(int)` into `_Z5printi` and `print(float)` into `_Z5printd`. 

## Inlining
Is a **suggestion** that we inject the code into the call site. 
Reducing overhead of the function call, however, obviously not guaranteed. 
```
inline void inlineFunc()
```

# Virtual Functions
## Dynamic Dispatch

Allowing for polymorphism. 
Dynamic Dispatch aka late binding, is where we look at the actual type of the object that the base pointer or base reference is pointing/referencing. 
`Shape* circle`, where `circle` is of `Circle` and `class Circle : public Shape`, here `Circle` has to be a `Shape`. 

Each class with virtual functions has a virtual table (vtable). Just an array of pointers to the virtual functions. Each object of such a class contains a pointer to the vtable. 
When we call, dynamic dispatch will find the right function to call. 
