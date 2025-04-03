A function that can suspend execution to be resumed later. Stackless : they suspend execution by returning to the caller, and the data that is required to resume execution is stored separately from the stack. 

Allowing for sequential code that executes asynchronously. 
So the idea is that we can run the function and then we can pause it and just resume it some other time. 

`co_await` -> suspends execution until an awaited operation completes
`co_yield` -> produces  value and suspends execution (useful for generators).
`co_return` -> ends a coroutine and returns a value

```
#include <iostream> 
#include <coroutine> 

struct ReturnObject{
	struct promise_type{ 
		int value; 

		ReturnObject get_return_object() { return {}; }
		std::suspend_never initial_suspend() { return {}; }
		std::suspend_never final_suspend() noexcept { return {}; }
		void return_value(int v) { value = v; }
		void unhandled_exception() { std::terminate(); }
	};
};

ReturnObject myCoroutine() 
{
	co_return 42;
}

int main() 
{ 
	auto coro = myCoroutine(); // Coroutine does not run yet
	std::cout << "Coroutine executed.\n";
}
```

When we run a coroutine - it does not run quite yet - it will return a handle to the coroutine. 

Execution starts only when it is resumed. 

The coroutine can then suspend itself. `co_await` `co_yield`. 

When finished then `co_return` provides a result. 

```
struct Generator { 
	struct promise_type { 
		int current_value;
		Generator get_return_object() { return Generat{std::coroutine_handle<promise_type>::from_promise(*this)};}
		std::suspend_always initial_suspend() { return {}; }
		std::suspend_always final_suspend() noexcept { return {}; }
		std::suspend_always yield_value(int value)
		{
			current_value = value;
			return {};
		}
		void return_void() {}
		void unhandled_exception() { std::terminate(); }
	};

	std::coroutine_handle<promise_type> handle;
	explicit Generator(std::coroutine_handle<promise_type> h) : handle(h) {}
	~Generator() (if(handle) handle.destroy(); )

	bool next() 
	{ 
		if(!handle || handle.done()) return false;
	}

	int value() const { return handle.promise().current_value;}
}

Generator countToThree() 
{ 
	for(int i = 1; i <= 3; ++i)
	{ 
		co_yield i; 
	}
}

int main() 
{ 
	auto gen = coutnToThree(); 
	while(gen.next())
	{ 
		std::cout << gen.value() << "\n";
	}
}
```


Coroutines were long established and C++ is late to the party. 
Stackfull and Stackless - C++ uses stackless - as it uses the heap in order to remember things about the coroutine. 

The Coroutine frame it's called - is put on the heap. 

We can replace function pointers and callbacks. 


![[Pasted image 20250227224035.png]]

After `co_return` it is finished. 
`co_await` more data from the outside. 

### Consisting Of: 

A wrapper type: the return type of the coroutine function's prototype: -> there's the wrapper type. 
The compiler will always look for a type with the exact name `promise_type` inside the return type of the coroutine (the wrapper type). 
So inside the wrapper needs to be a the `promise_type`. 

We can do this with type alias, or typedef, or just put the type directly inside the coroutine wrapper type. 
An awaitable type once we use `co_await. 
We also often use an iterator as well. 

Coroutines in C++ are finite state machines (FSM) controlled an customized by the `promise_type`. 

`promise_type` is a hard coded thing that the compiler is looking for. 

```
Chat Fun()  // Here Chat is the wrapper type containing the promise type
{ 
	co_yield "Hello!\n"s; // calls promise_type.yield_value

	std::cout << co_await std::string{}; // Calls promise_type.await_transform

	co_return "Here!\n"s; // Calls promise_type.return_value
}

void Use() 
{ 
	Chat chat = Fun(); // creation of the coroutine, it doesn't actually run, this gets the coroutine handle I believe
	
	std::cout << chat.listen(); // trigger the machine 

	chat.answer("Where are you?!\n"s); // send data into the coroutine

	std::cout << chat.listen(); // wait for more data from the coroutine. 
}
```
`Chat` must contain `promise_type`. 

```
// this is inside our wrapper type
struct promise_type 
{ 
	std::string _msgOut{}, _msgIn{}; // storing a value from or for the coroutine
	
	void  unhandled_exception() noexcept{} // what to do in case of an exception
	Chat get_return_object() {return Chat{this};} // coroutine creation
	
	std::suspend_always initial_suspend() noexcept { return {}; } // startup
	std::suspend_always yield_value(std::string msg){ 
		_msgOut = std::move(msg);
		return {};
	} // value from co_yield

	auto await_transform(std::string) noexcept // value from co_await, and std::string for overload resolution
	{  
		struct awaiter {  // customized version instead of using suspend_awlways or suspend_never
			promise_type& pt; 
			constexpr bool await_ready() const noexcept { return true; }
			std::string await_resume() const noexcept { return std::move(pt._msgIn);}
			void await_suspend()(std::coroutine_handle<>) const noexcept {}
		}

		return awaiter{*this};
	}

	void return_value(std::string msg) noexcept { _msgOut = std::move(msg); } // value from co_return
	std::suspend_always final_suspend() noexcept { return {}; } // ending
};
```

#### What is `std::suspend_always`
Is a type in `<coroutine>`, always suspends the coroutine
Always suspends the coroutine when used. 
I think it looks like this: 
```
struct suspend_always
{ 
	constexpr bool await_ready() const noexcept { return false; }
	constexpr void await_suspend(std::coroutine_handle<>) const noexcept {}
	constexpr void await_resume() const noexcept {}
};
```

```
struct MyCoroutine 
{ 
	struct promise_type
	{ 
		MyCoroutine get_return_object() { return {}; }
		std::suspend_always initial_suspend() { return {}; } // always suspend initially
		std::suspend_never final_suspend() noexcept { return {}; } // don't suspend at the end
		void return_void() {}
		void unhandled_exception() { std::terminate(); }
	};
};
```

`initial_suspend` return `std::suspend_always`  means that it will not run immediately when we call it. 

doing `std::suspend_always final_suspend() ... ` means that it will suspend at the end without getting destroyed automatically. 

```
// suspend never 
struct suspend_never {
    constexpr bool await_ready() const noexcept { return true; } // Always ready
    constexpr void await_suspend(std::coroutine_handle<>) const noexcept {} // Does nothing
    constexpr void await_resume() const noexcept {} // Does nothing
};
```

`std::coroutine_handle<>` is the handle to the coroutine where we can manipulate it from there. 
```
template <typename Promise = void> 
struct coroutine_handle 
{ 
	static coroutine_handle from_address(void* addr) noexcept; 
	static coroutine_handle from_promise(Promise& promise) noexcept;
	
	void resume() const;
	void destroy() const;
	bool done() const noexcept;
	Promise& promise() const;
};
```
