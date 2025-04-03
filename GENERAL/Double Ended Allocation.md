From reddit, every thread gets a stack, therefore having multi-threaded programs all using stacks is not that great. 

### Forming an allocation: 
**Using the stack**
```
/*---- INIT  ----*/
const size_t MEMORY_SIZE = 1024 * 1024; 
char memory[MEMORY_SIZE];
char* head = memory; 
char* tail = memory + MEMORY_SIZE;
```

**Allocation Functions**
```
void* allocateFromHead(size_t size) // we can use void* to cast to any pointer
{ 
	if(head + size <= tail)
	{ 
		void* result = head;
		head += size;
		return result;
	} else { 
		// Handle out of memory error as you see fit
		return nullptr;
	}
}

void* allocateFromTail(size_t size)
{ 
	if(tail - size >= head)
	{ 
		tai -= size;
		return tail;
	} else { 
		// handle out of mem error as you see fit
		return nullptr;
	}
}
```
