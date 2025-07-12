Copying an array is forbidden tech, therefore we cannot return one. 
We can return a pointer or a reference to an array. 

There are ways to simplify what looks to be an awkward way of returning a pointer to a function. 
Using a type alias is the easiest way. 

```
typedef int arrT[10];
// here arrT would be a synonym for the type array of ten ints
using arrT = int[10]; Another way of writing it
arrT* function(int i);
```
Here `arrT` is a synonym for an array of ten ints. 

The return type is a pointer to this type. 
The function that takes a single int argument and returns a pointer to array of ten ints.

#### Declaring A Function that Returns a Pointer to An Array
Without using a type alias, we must remember that the dimension of an array follows the name being defined. 
```
int arr[10];
int *p1[10]; // p1 is an array of ten pointers
int (*p2)[10] = &arr; // points to an array of ten ints
```

If we want to define and return an pointer to an array, the dimension must follow the function's name. 

The function has a parameter list, which also follows the name, and precedes the dimension. Therefore, the form of a function that returns a pointer to an array is: 
`Type (* function (parameter_list))[dimension]`. 

It's almost as if the name of the function and it's parameter_list is just part of the declaration. 

Type here is the type of elements that will be found in the array. 
Without (), we would be defining an array of pointers. 

Here is an example without type alias: 
`int (*func(inti))[10];`
Thinking about it as follows:
- `func(int)` says that we can call `func` with an int argument.
- `(*func(int))` mentions that we can dereference the result of that call
- `(*func(int))[10]` says that dereferencing the result of a call to `func`yields an array of size ten. 
- `int (*func(int))[10]` says the element type in the array we are pointing to is an `int`. 

#### Using a Trailing Return Type
There is yet another way of simplification by using a trialing return type. 

Trailing returns can be defined for any function, but are most useful for functions with complicated return types, such as pointers.

Like in python, the return type is put after the function, making it easier to see what we are returning. 

```
auto func(int i) -> int(*)[10]
```
Returning a pointer to an array of ten ints. 

To show that the return type follows the parameter list, we use auto where the return type would ordinarily appear. 

##### Using `decltype`
The following function returns a pointer to one of two array, depending on the value of its parameter. 

```
int odd[] = {1,3,5,7,9};
int even[] = {0,2,4,6,8};
// returns a pointer to an array of five int elements
decltype(odd) *arrPtr(int i)
{ 
	return(i%2)? &odd : &even;
}
```

The decltype means that the function returns a pointer to whatever type odd has. 
Meaning that the function as a whole will return a pointer to an array of five ints. 
REMEMBER: `declytype` will return type array, not type pointer that we have been accustomed to. 

Therefore we have to add the * in order to represent the pointer idea. 
