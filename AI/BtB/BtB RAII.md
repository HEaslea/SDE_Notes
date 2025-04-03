[The video in question](https://www.youtube.com/watch?v=Rfu06XAhx90)

![[Pasted image 20240414174827.png]]![[Pasted image 20240414174840.png]]
Leaking Resources - losing the handle of the resource that we created. 

![[Pasted image 20240414175637.png]]
A mutex is just a thing that prevents multiple threads accessing the same resource (bit of memory) due to the undefined times that the threads access that bit of memory. 

![[Pasted image 20240414213707.png]]

Sample Function: 
```
bool fn(std::mutex & SomeMutex, SomeDataSource & src)
{ 
	someMutex.lock();
	BufferClass buffer; // create some buffer class
	src.readIntoBuffer(buffer); // from src to buffer we just created
	buffer.display(); // read it out somehow
	// someMutext.unlock(); just unlock at some point before return
	return true; // ERROR
}
```

It's clear here that we didn't unlock the mutex, in some longer functions, this might not be as clear, however, here, it's obvious. 

Now what if we did something like this, where we check that the function was successful in reading to the buffer: 

```
bool fn(std::mutex & SomeMutex, SomeDataSource & src)
{ 
	someMutex.lock();
	BufferClass buffer; 
	if(not sr.readIntoBuffer(buffer)){ 
		// someMutex.unlock();
		return false;
	} // ANOTHER ERROR, returned before unlocking mutex
	buffer.display(); 
	someMutext.unlock();
	return true; 
}
```

An early return is rough here. 
However, what if reading into the buffer throws an exception, there is an error. 
We could use an exception swallower: 
```
bool fn(std::mutex & someMutex, SomeDataSource & src)
{ 
	someMutex.lock(); 
	try{ 
		BufferClass buffer; 
		if(not src.readIntoBuffer(buffer)) { 
			someMutext.unlock();
			return false;
		}
		buffer.display();
	} catch(...) { someMutex.unlock(); throw; } // exception swallower
	// to catch all exceptions
	someMutex.unlock();
	return true;
}
```
Even if display throws an exception, then we are still protected. 

![[Pasted image 20240415093843.png]]

The general thing with `RAII` is just that we acquire resource during the constructing of an object, and we dispose of it during the destructing of that object. 

![[Pasted image 20240415093939.png]]
Whoever owns the resource is the one that is responsible for its destruction properly. 

##### OWNERSHIP OWNERSHIP OWNERSHIP

### RAII Example Class: `std::lock_guard`
This is the standard RAII class to lock a single mutex during its construction and unlock it during its destruction. 

Here is the example again, using this RAII class idea (`std::lock_guard`): 
```
bool fn(std::mutex & someMutext, SomeDataSrouce & src)
{
	std::lock_guard lock{someMutex};
	BufferClass buffer;
	if(not src.readIntoBuffer(buffer)){ 
		return false;
	}
	buffer.display(); 
	return true;
}
```
Notice that we got rid of the try and catch, exception bs that we had before. 
The RAII will make sure that the mutex gets unlocked. 

##### Storage Durations
However the object start and end matters, doesn't have to be local objects. 
eg. creating a worker thread and pushing it into a vector, the lifetime of that object is dependent on the vector, however long it lives in the vector. 
removing it or deleting the vector, that's when the thread will get joined. 

```
void SomeClass::fn() { 
	auto worker { std::jthread{ []{ /* do something */ }}};
	m_vec.push_back(std::move{worker});
}
```

We've already experienced RAII when we looked at `std::unique_ptr` and `std::shared_ptr`. 

There are more: 
- `std::unique_lock`; more sophisticated `std::lock_guard` but you can unlock and relock it during its lifespan, and other things too
- `std::jthread` manages a joinable thread, will auto join during destruction
- `std::fstream` opens and closes the file (didn't know about this lol)


#### Reclaiming Responsibility
RAII classes might provide a way to get direct access to the enclosed resource. 
Some might provide a way to get the resource completely out of the class. 

We shouldn't really be messing with the resource on our own, just hand it off to some RAII class, in order to make sure that we don't fuck up completely lol.

#### RAII is not a Panacea
ie. it's not a solution to everything
There are other failure modes that RAII is not intended to solve: 
- Resource loops
- Deadlocks

Deadlocking is this weird thing with mutex's, where thread 1 will try to grab A, then the thread 2  tries to grab B, they're both successful. Then thread 1 tries to grab B, it can't, it's already held, so we move to thread 2, that tries to grab A, it can't, neither thread can go forward, they are just stuck. 
