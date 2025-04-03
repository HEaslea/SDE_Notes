Sync Primitive, allowing threads to wait for a certain condition to become true before proceeding. 

A thread will wait on a condition variable, releasing the associated mutex while waiting. 
Another thread signals the condition variable when the required condition is met. 

The waiting thread, wakes up, re-acquires the mutex, and continues execution. 

`wait()` release the mutex and blocks until notified

`notify_one()` wakes up one waiting thread. 

`notify_all()` wakes up all waiting threads. 


```
std::mutex mtx; 
std::condition_variable cv;

bool ready = false;

void worker() 
{ 
	std::unique_lock<std::mutex> lock(mtx);
	cv.wait(lock, [] {return ready;}); // wait until 'ready' is true
	std::cout << "Worker thread proceeding...\n";
}

void set_ready() 
{ 
	std::this_thread::sleep_for(std::chrono::seconds(1)); // simulate work
	{ 
		std::lock_guard<std::mutex> lock(mtx);
		ready = true;
	}
	cv.notify_one(); // signal the waiting thread
}
```

Found in the `<condition_variable>`
When we have the producer consumer relationship, sender-receiver relationship. 
Condition variable makes the thread wait till it is notified by the other thread. 
Used with mutexes to block access to the shared resource when one thread is working on it. 

`std::condition_variable variable_name;`

`wait()` this thread waits till the condition variable is notified. 

`wait_for()` this thread waits for a certain period of time, if notified before, the thread will awake anyways. 

`notify_one()` notify one of the waiting threads that the share resource is free to access, the selection is random. 

`notify_all()` notify all the threads. 

```
mutex mtx; 
condition_variable cv;

bool data_ready = false;

void producer()
{ 
	this_thread::sleep_for(chrono::seconds(2));

	// lock release
	lock_guard<mutex> lock(mtx);
	
	data_ready = true;

	cout << "Data Produced!" << endl;

	// notify consumer when done
	cv.notify_one(); 
}

void consumer() 
{ 
	// lock
	unique_lock<mutex> lock(mtx);

	cv.wait(lock, []{return data_ready;});

	
}
```

`Thread-A` - waits on a condition variable `cv.wait()`, will release a mutex while waiting. 
`Thread-B` modifies the shared data and calls `cv.notify_one()` (which i believe is random) or `cv.notify_all()`. 
`Thread-A` wakes up, reacquires the mutex, and checks the condition. 


