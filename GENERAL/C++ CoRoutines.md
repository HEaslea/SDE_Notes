These are functions that can be suspended and resumed later on. 

THEY ARE STACKLESS: they suspend execution by returning to the caller, and the data that is required to resume execution is stored separately from the stack. 

Allowing for sequential code that executes asynchronously. 

```
generator<usnigned int> iota(unsigned int n = 0)
{ 
	while(true)
		co_yield n++;
}
```
The coroutine will run indefinitely, returning a previously incremented `n` every time. 


