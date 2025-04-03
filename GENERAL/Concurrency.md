[Tour Explanation](https://isocpp.org/files/papers/5-Tour-Util.pdf)

A computation that can potentially be executed concurrently with other computations a **task**. 
A **thread** is a system level representation of a task in a program. 
To launch a task that works along side another is launched by using `std::thread`. 
Found in `<thread>`. 
A task here would be a function or a function object. 

```
void f(); // function 

struct F{  // function object
	void operator()(); // F's call operator
};

void user()
{ 
	thread t1{f}; // f() executes in a separate thread
	thread t2{F()}; // F()() executes in separate thread

	t1.join(); // wait for t1
	t2.join(); // wait for t2
}
```

`join()` here means that we wait for these two to terminate before leaving `user`. 

The join means, "wait for thread to terminate". 

Threads will share a single address space. 
They differ from processes, which generally do not directly share data. 

As this is the case, they can communicate through this shared space and shared objects. 

Mostly this will be controlled by locks, or other mechanisms to prevent data races (uncontrolled concurrent access to a variable) ie. so that it doesn't change the variable at the same time and cause something awkward etc. 
Imagine if we did something like this: 
```
void f() { cout << "Hello"; }

struct F{ 
	void operator()(){cout << "Parallel World\n";}
};
```

Here f and F are using the `cout` object without any form of synchronization. 
Undefined error here. 

So we might do something like this : 

```
void f(vector<double>& v); // function do something with v

struct F{ 
	vector<double> v; 
	F(vector<double>& vv): v{vv} {}
	void operator()(); 
};

int main(){ 
	vector<double> some_vec{1,2,3,4,5,6,7,8,9};
	vector<double> vec2{10,11,12,13,14};

	thread t1{f, some_vec}; // f(some_vec) will execute in a sep thread
	thread t2{F{vec2}}; // F(vec2)() executes in a separate thread

	t1.join(); t2.join();
}
```


Passing `vec2` by value will eliminate the risk of using the same vector from two different threads. 

### Returning Results
