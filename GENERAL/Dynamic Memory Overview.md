[[Dynamic Memory and Smart Pointers]]
[[Managing Memory Directly]]






Most of what we have written so far are objects and variables that have a defined lifetime. 
Global objects are allocated at program start-up and destroyed when the program ends. 
Local, automatic objects are created and destroyed when the block in which they are defined is entered and exited. 
Local `static` objects are allocated before their first use and are destroyed when the program ends. 

In addition to supporting automatic and `static` objects, we can allocate objects dynamically. 
They have a lifetime that is independent of where they are created; they exist until they are explicitly freed. 

This is a rich source of bugs. 
To make this safer, the library defines two smart pointer types that manage dynamically allocated objects. 
Smart pointers ensure that the objects to which they point are automatically freed when it is appropriate to do so. 

So far we have used only static or stack memory. 
Static memory is used for local `static` , for class `static` data members, and for variables that are defined outside any function. 
Stack memory is used for non`static` objects defined inside functions. 

They are created and destroyed automatically by the compiler. 
Stack objects exist only while the block in which they are defined is executing: `static` objects are allocated before they are used, and they are destroyed when the program ends. 

#freestore #heap

Every program has a pool of memory that it can use. This is the **free store** or **heap**.
The heap is for when the program allocates at run time. 
The program controls the lifetime of dynamic objects; our code must explicitly destroy such objects when they are no longer needed. 

