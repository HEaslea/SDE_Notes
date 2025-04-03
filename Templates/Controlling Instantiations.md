The fact that instantiations are generated when a template is used, means that the same instantiation may appear in multiple object files. 
In large systems, the overhead of instantiating the same template in multiple files can become significant. 

Under the new standard, we can use the `explicit instantiation` in order to reduce that overhead. 

```
extern template declaration; // instantiation declaration
template declaration; // instantiation definition
```

```
// instantiation delcaration and definition
extern template class Blob<string>; // declaration
template int compare(const int&, const int&); // definition
```
When the compiler will see the `extern` template declaration, it will not generate code for that specific instantiation in that file. 
This is a user promise to the compiler that there will be, somewhere, a non`extern` use of that instantiation elsewhere in the program. 
There might be several `extern` declarations for a given instantiation but there must be exactly one definition for that instantiation, somewhere. 

As the compiler will automatically generate instantiations of templates when we use it, the `extern` declaration **has to be before any use including that instantiation**. 

```
Application.cc: 

// these templates types must be instantiated elsewhere in the program
extern template class Blob<string>;
extern template int compare(const int&, const int&);

Blob<string> sa1, sa2; // instantiation will ppear elsewhere 

// Blob<int> and its initializer_list constructor instantiated in this file
Blob<int> a1 = {0,1,2,3};

Blob<int> a2(a1); // copy constructor instantiated in this file too 

int i = compare(a1[0], a2[0]); // instantiation will appear elsewhere
```

The file `Application.o` will contain instantiations for `Blob<int>`, along with the `initializer_list` and copy constructors for that class. 

The `compare<int>` function and `Blob<string>` will not be instantiated in this same file. 

There must be definitions for these in other files for the program: 
```
templateBuild.cc
// instantiation file must provide a (nonextern) definition 
// for every type and funciton that other files declare as extern
// and there must be some form of linkage here too
template int compare(const int&, const int&)

template class Blob<string>; // isntantiates all members of the class template
```

When the compiler sees an instantiation definition (as opposed to a declaration), it generates that code, rather than realises a reference to another file. 
`templateBuild.o`  will contain the definitions for `compare` instantiated with `int` and for the `Blob<string>` class. 

We must link `templateBuild.o` to `Application.o` when we link. 


> For every instantiation declaration, there must be an instantiation definition, somewhere, linked to the declarations


#### Instantiation Definitions Instantiate All Members
An instantiation definition for a class template instantiates **ALL** members of that template including inline member functions. 
When the compiler sees an instantiation definition, it can't know which member functions the program uses. 
Even if we don't use that member, that member will still be instantiated (seems obvious outside optimization). 
Therefore, we can use explicit instantiation only for types that can be used with all the members of that template: 
> An instantiation definition can be used only for types that can be used with every member function of a class template. 

### Efficiency and Flexibility
The lib smart pointer types, offer a good illustration of design choices face by designers of templates. 

The main difference between `shared_ptr` and `unique_ptr` is the strategy used in managing the pointer they hold . 
They also differ in how they let the user override their default deleter. 
Overriding `shared_ptr` by passing a callable object when we create or `reset` the pointer. 
In contrast to `unique_ptr` where the deleter is part of the type of a `unique_ptr` object. 

These differences might have an important performance impact. 

#### Binding the Deleter at Run Time
`shared_ptr` will have to access the deleter indirectly, that's why we have to supply a pointer to a function. 

We can be fairly sure that `shared_ptr` will not hold the deleter as a direct member, as the type of the deleter is not known until run-time. 

We can even, then, change the type of `deleter` during the `shared_ptr`'s lifetime, using `reset`. 
This is why the deleter must be stored indirectly. 

Say that `shared_ptr` holds the pointer to a function that deletes as a member: 
Then we the `shared_ptr` destructor must contain a statement such as this: 
```
// value of del known only at run time, call through a pointer
del ? del(p) : delete p;
// del(p) requires run-time jump to del's location
```


#### Binding the Deleter at Compile Time
`unique_ptr` here. 
The deleter is part of the type, not something that we reference: 
That is `unique_ptr`, one representing that pointer that `unique_ptr` manages and the other that represents the type of the deleter. 
The type of the deleter is now part of the type of the `unique_ptr`, the type of the deleter is known at compiler time. 
The deleter can be stored directly in each `unique_ptr` object. 

The `unique_ptr` destructor operates similarly to its `shared_ptr` counterpart in that it calls a user-supplied deleter or executes `delete` on its stored pointer: 
```
// del bound at compile time; direct call to the deleter is instantiated
del(p); // no run-time overhead
```

 `del` is either user-supplied type or default deleter. It doesn't matter, either way, the code that will be executed is known at compile-time. 
The call might even be inlined at compile-time, wouldn't that be nice. 

Binding the deleter, and most things, at compile-time, avoids the run-time overhead of indirectly calling its deleter. 
However, run-time indirect calling allows the user to override the deleter, and change what they need for flexibility's sake. 

