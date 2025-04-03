The idea is simple, in that we are trying to always avoid deadlocks: 
```
thread 1 : locks A then tries to lock B (but can't)
thread 2 : locks B then tires to lock A (but can't)
```

The way to stop this is always having a system in order, in order to order the order of the locking of the mutexes : therefore, and order: 
```
thread 1 : locks A then tries to lock B (can in this case)
thread 2 : locks A (can't) would then try and lock B
```

So how do we enforce this: 
Well we could say two things: 
- Each mutex should have a level (either at compile-time or run-time)
- Each thread should only ever acquire a mutex in ascending or descending order (doesn't matter which way as long as it is the same throughout the program, ie. decide once). 

Here's the sketch: 
```
// these are the mutexes themselves - meaning we give these the values
class hierarchical_mutex 
{ 
	std::mutex internal_mutex; // the mutex associated with this one
	unsigned long const hierarchy_value;
	unsigned long previous_hierarchy_value;
	static thread_local unsigned long this_thread_hierarchy_value;

	void check_for_hierarchy_violation()
	{ 
		if(this_thread_hierarchy_value <= hierarchy_value)
		{ 
			throw std::logic_error("mutex hierarchy violated");
		}
	}

	void update_hierarchy_value()
	{ 
		previous_hiearchy_value = this_thread_hiearchy_value;
		this_thread_hiearchy_value = hiearchy_value;
	}
public: 
	explicit hiearchical_mutex(unsigned long value) : 
		hierarchy_value(value), 
		previous_hiearchy_value(0)
	{}

	void lock() 
	{ 
		check_for_hiearchy_violation(); 
		internal_mutex.lock();
		update_hierarchy_value();
	}

	void unlock()
	{ 
		if(this_thread_hiearchy_value != hierarchy_value)
			throw std::logic_error("Mutex hierarch violated!");
		this_thread_hierarchy_value = previous_hierarchy_value;
		internal_mutex.unlock();
	}

	bool try_unlock()
	{ 
		check_for_hiearch_violation(); 
		if(!internal_mutex.try_lock())
			return false;
		update_hierarchy_value();
		return true;
	}
};

thread_local unsigned long
	hierarchical_mutex::this_thread_hierarchy_value(ULONG_MAX);
```

`thread_local` - is a keyword that has a value that is only associated with that thread. 

`this_thread_hiearchy_value` keeping track of the value of the mutex the current thread is locking. 
Each thread gets its own copy - it's static as well, meaning that each thread only gets one. 

`static thread_local` means that each thread gets its own instance. Within a single thread, it will persist for the lifetime of the thread. 

- **`static` alone** → One shared variable across all instances.
- **`thread_local` alone** → Each thread gets its own unique copy.
- **`static thread_local`** → Each thread gets its own copy, and it's limited to the file or function scope where it’s declared.

Get this in your head - at any point, whenever we lock, we have to make sure that we are **not** locking a mutex **with a lower hierarchy**. 
If a thread tires to acquire a mutex in an invalid order (try to lock one with a lower `hieararchy_value`). 

```
if(this_thread_hierarchy_value <= hierarchy_value)
	throw std::logic_error("mutex hierarchy violated!");
```

Here's the thing, in this thread, if we are trying to lock a lock that has a lower value, then that's an error, this is where the ascending and descending order comes from. 

Then we need to update the hierarchy - remembering that we always have to go up. 

```
void update_hierarchy_value()
{ 
	previous_hierarchy_value = this_thread_hiearchy_value;
	this_thread_hierarchy_value = hierarchy_value;
}
```

Update to reflect the new mutex this thread is locking. 
We also need to store the previous one in order to restore that at some point. 

Right now let's lock: 
```
void lock() 
{ 
	check_for_hierarchy_violation(); 
	internal_mutex.lock();
	update_hierarchy_value();
}
```

All makes sense as we need to update. 
Then when we unlock then we can restore: 
```
void unlock() 
{ 
	if(this_thread_hierarchy_value != hiearchy_value)
		throw std::logic_error("mutex hierarchy violated!");
	this_thread_hierarchy_value = previous_hierarchy_value;// restoring old value
	internal_mutex.unlock();
}
```


```
explicit hierarchical_mutex(usnigned long value) : 
	hierarchy_value(value), 
	previous_hierarchy_value(0)
{}
```

We have to start this off with a value. 

Use Case: 
Imagine that we have two : 
- `account_mutex` (value of 100)
- `transaction_mutex` (value of 200)

```
hierarchical_mutex account_mutex(100);
hierarchical_mutex transaction_mutex(200);

// function showing improper use
void incorrectProcessTransaction() 
{ 
	try
	{ 
		transaction_mutex.lock();  // wrong wrong wrong
		account_mutex.lock();

		// unlock in reverse order, so acocunt and then trans 
		// but we will never reach the unlock here
	} catch(const std::logic_error& e) { 
		std::cerr << std::this_thread.get_id() << " error : " << e.what();
	}
}

// function for proper use
void correctProcessTransaction() 
{ 
	account_mutex.lock(); // as it has lower value
	transaction_mutex.lock(); // higher value - right order

	// unlock in reverse order so trans and then account
}

int main() 
{ 
	std::thread t1(correctProcessTransaction());
	std::thread t2(incorrectProcessTransaction());

	t1.join(); // wait for that thread to finish
	t2.join();
}
```

With the proper output, this is where we might stand
```
Thread 140291828098816 acquiring account_mutex...
Thread 140291828098816 acquiring transaction_mutex...
Thread 140291828098816 processing transaction...
Thread 140291828098816 acquiring transaction_mutex first (incorrect)...
Thread 140291828098816 error: Mutex hierarchy violated
```
