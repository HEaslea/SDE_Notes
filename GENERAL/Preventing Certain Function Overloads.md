Say that we have this function: 
`bool isLucky(int a);`

We know that (due to C's heritage) will mean that anything vaguely like an `int` will be converted to an `int`: 
- `bool`
- `char`
- `float`
etc.  etc.etc.etc.etc.etc.
However, what if we only want `int`s involved here. 

It's like the opposite of template specialization:
**Any Function Can Be Deleted**

```
bool isLucky(int a); 

bool isLucky(char) = delete; 

bool isLucky(bool) = delete;

bool isLucky(double) = delete;
```

This is also true for templates: 

We might want to process pointers in some way, however, we don't want certain pointers to be passed in.
So we might have a template: 
```
template<typename T> 
void processPointers(T* ptr); 

// let's reject certain passes
// We will have to template specialize in order to deny certain calls
template <>
void processPointer<void>(void*) = delete;

template<>
void processPointer<char>(char*) = delete;
```
Remember that `<>` template parameterization here, we have to put in the type that we are passing in not `<char*>`. 

Interestingly, you cannot delete a template specialization that is a different access level to the main;