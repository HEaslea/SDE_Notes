Separation of concerns and performance. 

#### Separation of Concerns
Keeping related bits of code together, and unrelated away from certain topics, we are cleaning up our code. 
Think of the idea that if you have a data sharing system in the process data space, imagine the idea that if you want to "do something" you will have to call a function or similar, flow goes somewhere else. However, imagine that thing is "doing" in another thread, and we just share that resource (safely). 
We no longer need to remove flow from the what we were doing originally, have I just described concurrency, yes, but abstractly, we remove what might be a dull function, run it parallel, remove it conceptually too, get the info (safely). 
Otherwise we might have to use a task_switching software. 

Check a DVD player, we might need multiple threads in order to read, and output data at a fast rate. 

Threads are usually divided, based conceptually, rather than the number of cores on our CPU. 

Remember that threads are constantly running, even when you are idle, and on multi-threads, if one is idle for input, then we might use another for beefy functions etc. etc.

### Performance
"*If software is to take advantage of this increased computing power, it must be designed to run multiple tasks concurrently*". 

Herb Sutter - "The free lunch is over". 

Dividing a single task int parts, run each in parallel, **task parallelism**. 
There is also the idea of **data parallelism**, performing the same operation on multiple data areas at a time. 

**Embarassingly parallel**: the idea that some things just solve themselves as soon as we utter the idea of parallelism. 
*AKA conveniently concurrent or naturally parallel*. 

"Many hands make light work" - me, right now. 


## WHEN DO WE NOT USE
Fundamentally, the time to use concurrency, is when the cost is worth the benefit. 

In many cases, such code is harder to understand, is unnecessary.

If the performance increase is not that great, than don't use it. 

There is an overhead in launching a thread that we don't really think about. 

The OS has to allocate kernel resources, and stack space, and then add the new thread to the scheduler, all of which takes some times. 

If the task on the thread is very small, then what was the actual point, we are just making things take longer that we could solve sequentially. 

Using too many threads can be an issue as well. The whole system (OS) could run much slower. 

Even 64 have finite address spaces, 32 bit has a 4GB expression limit for address space. 

Thread pools will solve this issue. 

Imagine if you hhad a thread for every single connection to a server. Over time, with more and more connections there would be more and more issues due to limited resources, we would also have to keep swapping things in and out, almost every other cycle. 

Remember that we have to context switch as well. 

It's a double-edged sword. 

### Hello World
```
#include <iostream> 
#include <thread> // here we go

void hello()
{ 
	std::cout << "Hello Concurrent World\n";
}

int main()
{ 
	std::threat t(hello);
	t.join();
}
```

`#include <thread>` there you go, that's it, we're done here now. 

Every single thread has to have an **initial function**, which is where that new thread begins execution, we have to explicitly say where we want that thread to begin. 

For the initial thread, it has `main()`. 
The others, it is specified in the constructor of thread. 

Once this has launched, we have two threads, *main* and `t`. 

The thing is, that main obviously continues processing, meaning that it could finish up before waiting for `t` to finish. 

This is why we call `join`.  This means that the calling thread, will wait for the thread associated with `t` to join it before moving on. 
 That is chapter 1. 
 