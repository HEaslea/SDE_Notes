When we compile a program, the compiler stores the final `.exe` in some hard drive / some disk. 
In order to run, its instructions are loaded into the RAM, then executed. 
This does not mean that all the program is in RAM. 

The environment that is responsible for running and monitoring programs plays the main role. 

The host environment, being the OS. 
The OS loads the contents of the program (instructions and some data, the process, as in the running program). 

Before we do anything major, we need to use **Virtual Memory**, making it possible to handle processes conveniently and to share resources between processes. 

Whenever we refer to the memory that a process is loaded into, we mean the virtual memory, which, in turn, maps its contents to the RAM. 

As we know, memory is a lot of boxes, each storing a certain amount of data. 

They are memory cells, considering that each cell can store 1 byte, 8 bits. 

Addressing cells - unique memory address. 

Starting at 0. 

![[Pasted image 20250805135948.png]]


### Why Have that Extra Layer of Abstraction
Why would you even need that extra layer of abstraction. 
This is for the ease of managing processes and providing more functionality than the physical memory. 
The physical memory, in RAM, is just the addresses and of course there is data in those memory cells. 

Think about a game, that takes 2GB (you would be so lucky), and we only have a 512 MB (you would be so unlucky). 
Virtual memory allows the OS to load the program portion by portion, by unloading parts from RAM and mapping new parts. 
It allows us to swap things over. 

There is also support for having more than one program in memory, thus, we have parallel (pseudo-parallel) execution of multiple programs. 

And great use for shared code and data, such as dynamic library. 

Whenever two different programs require the same library to work with, a single instance of the library could exist in memory, and be used by both programs without them knowing about each other. 

![[Pasted image 20250805141111.png]]


This means that we can map in any way that we want. 

#### Addressing
Each cell has a unique address.
Address usually is represented by a **hexadecimal** form, as it's shorter and it's faster to convert into binary rather than decimal numbers. 

A program that is loaded into virtual memory, operates and sees logical addresses. 
These are AKA virtual addresses, they are fake and provided by the OS and will be different per OS and perhaps every time we turn the machine on. 
They translate them into physical addresses when needed. 

There is a layer of optimization for this: 
The CPU provides a translation lookaside buffer, a part of its MMU, memory management unit. 
This is where it caches recent translations of virtual addresses to physical addresses. 

32-bit and 64-bit systems, the size of the addresses. 

#### Data Types
Quick note on data types here, as I don't really know where else to put it. 

C++, you can obviously never change the type of a var at run-time. 

Whenever we use `auto` - that is deduced at compile time, similar to a template. 

The compiler, for the most part, needs to know the actual size of the variable beforehand. 

Knowing the size is important to generate the final machine code because accessing a variable requires its address and size, for the final machine code. 

We need to know the size so that we can allocate. 

There are 
- **Fundamental types**: `int double void char`
- **Compound types**: `pointers arrays classes`



![[Pasted image 20250805222958.png]]

Remembering them is easy, as they just go into each other, with heap being in the middle of the code/process. 

```
int add(int a, int b) { return a + b; }

int subtract(int a, int b) { return a - b; }

int multiply(int a, int b) { return a * b; }

int divide(int a, int b) { return a / b; }

int main(){ 
	std::unordered_map<char, int (*)(int, int)> operations; // map of char and function of return int and taking in two ints

	operations['+'] = &add;
	operations['-'] = &subtract;
	operations['*'] = &multiply;
	operations['/'] = &divide;

	// read some input, this is a great way to map using a char
	char op;
	int num1, num2;
	std::cin >> num1 >> num2 >> op;

	std::cout << operations[op](num1, num2);
}
```

And remember that a word is usually the bit size of the machine, so 32 or 64 bit. 

