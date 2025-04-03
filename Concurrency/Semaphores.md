Synchronization Primitives used as a counter to limit the number of threads that can be working on a certain resource at a time. 

Used regularly in resource pools (database connections, worker threads). 

`acquire()` decreasing the count and `release()` will increment it. 


```
std::counting_semaphore<3> sem(3); // Max 3 threads can access at a time

void worker(int id)
{ 
	sem.acquire(); // try to enter
	...
	sem.release(); // once done we increment the counter
}

int main() 
{ 
	std::thread threads[6]; // more threads than allowed, this could be a resource pool

	for(int i = 0; i < 6; ++i)
		thread[i] = std::thread(worker, i);
	// this will make 6 threads very quickly, however, they might be blocked in the worker();
		
	for(auto& t : threads)
		t.join(); // will block until i thread is finished
}
```

The thing with semaphores is that we are not owning them, like we do with mutexes. 
Limiting access (thread pool). 

Think of them as bouncers to a nightclub: When a number of people enter, that might be the limit and no one else is allowed in, however, once we have someone leave, then we are allowed to let someone in, if 2 leave, 2 can enter etc. etc.

[Difference Between Mutex and Semaphore](https://barrgroup.com/blog/mutexes-and-semaphores-demystified)

Mutexes at their core are binary flags used to protect a shared resource by ensuring mutual exclusion inside critical sections of code. They help serialize multiple task's access to shared data. They are taken and released, always in that order, always by the same thread. 

The correct use of semaphores is the signalling from one task to another. They are not always taken and released by the same thread in that order. 

In contrast, here's how a mutex works: 
```
// Task 1
	mutexWait(mutex_mens_room);
		// safely use shared resource
	mutexRelease(mutex_mens_room);

// Task 2
	mutexWait(mutex_mens_room);
		// safely use shared resource
	mutexRelease(mutex_mens_room);
```

Semaphores, should always be used to send a signal: 
```
// Task 1 Producer
	semPost(sem_power_btn); // send the signal

// Task 2 Consumer
	semPend(sem_power_btn); // wait for signal
```

`sem.acquire()`- blocks if count == 0
```
if(sem.try_acquire())
{ 
	// successfully got it
} else 
{ 
	// failed to get so do something else that is really cool
}
```

```
if(sem.try_acquire_for(std::chrono::seconds(2)))
{ 
	// acquired within 2 seconds
} else 
{ 
	// timeout occurred, longer than 2 seconds
}
```

#### Producers and Consumers
Two key semaphores: 
1. Items available - tracks produced items (consumer waits on this)
2. Space available - tracks empty slots (producer waits on this)

```
std::couting_semaphore<N> items(0); // no items produced at the beginning
std::couting_semaphore<N> spaces(N); // all the slots open now
```

```
// producer thread (adding items)
void producer() 
{ 
	for(int i = 0; i < 10; i++)
	{ 
		spaces.acquire(); // wait for space to be available block(if full)
		buffer.push(i); // add item to the buffer
		items.release(); // signal that an items is available
	}
}

// consumer (removiting items)
void consumer() 
{ 
	for(int i = 0; i < 10; i++)
	{ 
		// the second semaphore
		items.acquire(); // wait for an item to be available
		buffer.pop(); 
		spaces.release(); // signalling there are more spaces now
	}
}
```