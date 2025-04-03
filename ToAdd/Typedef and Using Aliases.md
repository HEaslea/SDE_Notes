Here is a great example: 

```
using ip = int*; // ip is a synonym for int pointer
```
This method reads a lot like English. 

```
typedef int *ip; 
typdef int* ip; // both of these work
```
This means exactly the same thing, that ip is a synonym for int pointer. 

```
using vi = vector<int>; 
typdef vector<int> vi; 
```
They are just reversed ways of writing each other. 

`using` and then the user idea of the type. 
`typedef` and then the program's type. 

`using VII = vector<int>::iterator;`
`typedef vector<int>::iterator VII;`

Obviously typedef doesn't have an initialization, and (=). 