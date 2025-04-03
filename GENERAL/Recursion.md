Function calling itself, either directly or indirectly, is a recursive function. 
Rewriting the factorial function to use recursion: 
```
int factorial(int val)
{ 
	if(val > 1){ 
		return factorial(val-1)*val
	}
	return 1;
```
say we pass 4 in by value; 

val > 1; therefore we call `factorial(3)` which will be `*val` = `*4` later on. 

Of course the function must have a base case, that isn't a recursive call to itself, otherwise it would just go on forever. 

The main function cannot call itself obviously. 

