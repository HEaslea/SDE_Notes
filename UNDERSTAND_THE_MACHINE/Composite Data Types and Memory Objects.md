Refresher - POD - Plain Old Data Type. 
A struct or a class that has no user-defined constructors, destructors or virtual functions. 
Only public members. 
Contains only POD members. 
Trivially Copyable and Standard-layout

```
struct myData{ 
	int x; 
	float y;
};
```

##### A Very out of Place, Full Refresher of Virtual Functions
Remember that we use the `virtual` keyword in the base class, that can be `overridden` in the derived class, and it dynamically dispatched at run time. 
The hallmark of polymorphism. 
This is so that we can have a ref or a pointer to the base class, and we get the right function. 

```
class Base
{ 
public: 
	virtual void speak() 
	{ 
		std::cout << "Base Speaking\n";
	}
};

class Dervied : public Base
{ 
public: 
	void speak() override // override is optional 
	{   
		std::cout << "Derived Speaking\n";
	}
};

void doSpeak(Base& b)
{ 
	b.speak(); // dynamic dispatch
}

int main() 
{ 
	Derived d; 
	doSpeak(d); // prints : Derived Speaking
}
```

Remember to always use `override`, that will create proper errors if there are any, and is explicit to the reader what is gong on there. 
`final` will not allow us to `override` anymore in the next derived class. 

**Virtual vs Non-virtual**
```
class Base
{ 
public:
	void foo() { std::cout << "Base foo\n"; }
	virtual void bar() { std::cout << "Base bar\n"; }
};

class Derived : public Base 
{ 
public: 
	void foo() { std::cout << "Derived foo\n"; }
	void bar() override { std::cout << "Derived bar\n"; }
};

Base* b = new Derived; 
b->foo(); // Base foo (not Virtual)
b->bar(); // Derived bar (Virtual)
```

Remember that making a pure virtual function, will make a class **abstract** : meaning that we cannot make an object of that class. If you read some of the design patterns, this abstract class is the defining feature for most of them. 

Virtual Destructors, to ensure that the derived class destructor is called when the object is destroyed. 

Static dispatch is dependent on the pointer type, not on the type that the pointer is pointing to (dynamic dispatch). 

Pure Virtual - `virtual void draw() = 0; // pure virtual`

**Object Slicing**a: When we slice, we are not dynamic dispatching, we are literally only copying part of the full object. We have a derived class instantiated object - then we are cutting that object, so that we only get the base (or base classes). Can be sliced in any way (so in any way about the tree hierarchy). 

```
Derived d; 
Base b = d; // Object slicing - we are only copying the Base part
b.speak();
```
This means that we are only using the `Base` copy constructor. 

However, if we reference or have a pointer to, there is no slicing. 

Onwards. 


## Pointers 
Variable whose value refers to some other object. 
Most HLL's hide the complexity of the pointer behind abstractions. 

Take an array : Consider the array in Pascal: 
`M: array [0...1023] of integer`
Here we can have 1024 different integer variables, that are not accessed by name, but rather just by an index. 

Obviously `i := 0; (* Assume i is an integer variable *)`
`M[i] := 100;`

We can do any arithmetic in order to get the right index, essentially getting the correct variable name. Any time that you need to access some variable, that depends on something arithmetic, we can map using this idea here. 

Cooler things begin to happen when we: 

```
M[1] := 0; 
M[M[1]] := 100;
```
`M[M[1]] --> M[1]`here is the idea of a pointer. 

## Pointer Implementation 
Pointers are really just memory addresses and an abstraction on top of that. 
A language could define a pointer using any mechanism that maps the value of the pointer to the address of some object in memory. 

Some implementations, like Pascal, use offsets from some fixed memory address. 

Some, like LISP (some language I've never heard of that) - implement pointers by using **double indirection** : that is, the pointer object contains the address of some memory variable who value is the address of the object to be accessed. 

In C++ : they are typically implemented as raw memory addresses - typically machine addresses (integers) that point to the location in memory. 

in C++ again : pointers are 4 bytes on 32-bit system
8 bytes on a 64 bit system. 

For the rest of the chapter, we will use this simplified idea, where a pointer is a variable whose value is the address of some other object in memory. 


On a 32 bit 80x86 machine instruction: 
```
mov( PointerVarible, ebx ); // load the pointer variable into a register
mov( [ebx], eax ); // use the register indirect mode to access data
```

Access to data via double indirection, although possible, is less efficient than the straight pointer implementation because it takes an extra machine instruction, and possibly another read, to fetch the data from memory. 

For clarity - 
```
int x = 42; 
int* prt1 = &x
int** prt2 = &ptr1; // saying that this is a poitner to a pointer
```

Relative to direct address : accessing `x` directly or even via a single pointer `*ptr1` is faster than `**ptr2`.
Each pointer level involves a memory lookup : - cache miss : - extra indirection prenalties. 

For performance critical paths, avoiding indirection is better. 

Double indirection in ASM: 
```
mov( hDblPtr, ebx ); // get the pointer to a pointer
mov( [ebx], ebx ); // get the pointer to the value
mov( [ebx], eax ); // get the value
```
There is more code per double indirection here. 

### Pointers and Dynamic Memory Allocation
Pointers typically reference anonymous variables that you allocate on the **heap**. 
Heap - a region in memory reserved for dynamic storage allocation. 

We have to use memory allocation and deallocation (and their abstractions like `new`) `malloc()/free()` in C, `new()/dispose()` in Pascal, `new()/delete()` in C++ (however, we should be using smart pointers after C++11). 

Some languages will handle this on their own, and have garbage collection in order to do that. 

Objects that are on the heap are known as **anonymous variables**, as you have to refer to them through their address, rather than by a name. 

Store the result from the allocation variable, which is a pointer (of some kind), the pointer might have a name, but the object itself does not. 

## Pointer Operations and Pointer Arithmetic
Most languages will allow you to assign addresses to pointer variables. 
Most allow for comparing pointer values, for equality or inequality, and indirectly reference an object via a pointer. 

Limited arithmetic with pointers. 
Add an integer constant to, or subtract one, from a pointer. 

`ptrVar = malloc( bytes_to_allocate );`

A "good" C programmer will usually supply an expression like`sizeof(int)` as this parameter. 

`sizeof()` returning the number of bytes needed by its single parameter. 

`ptrVar = malloc( sizeof( int ) * 8);`


