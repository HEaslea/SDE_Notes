Commonly used to enable the observer pattern. 
[Events and Delegates](https://www.youtube.com/watch?v=e4G8VgqdaD4)
Create an event, have other classes subscribe to that event, then we can `Invoke()` that event, and the listeners will have  ball, just like the Observer pattern;

## Delegates
Is a type that represents a method signature. 
Allowing methods to be passed as parameters, assigned to variables, and called indirectly. They are "like" `std::function`, they both encapsulate method references, allowing them to be passed around. 
They are type safe function pointers, meaning that the method that they point to will match a specific signature: 
```
// They are variables similar to std::function
public delegate void MyDelegate(int value);
```

This delegate `MyDelegate` can point to methods that take in an `int` parameter and return `void`.
Then using one: 
```
public void MyMethod(int value)
{ 
	Console.WriteLine("Value " + value);
}

public void Example()
{ 
	MyDelegate del = MyMethod;
	del(42);
}
```

## Events
Well an Event is just a special kind of delegate: 
That is used to provide notifications to other parts of a program when something of interest occurs: 
ie. Observer patterns, objects, the subject sends notifications to observers (listeners). 
The pattern is sort of written into C#. 

```
public delegate void MyEventHandler(int value);

public event MyEventHandler MyEvent;
```
The delegate type has to match the event's method signature. 
`MyEvent` is an event based on `MyEventHandler` delegate. 

### Raising (Invoking) An Event
It's wise to check to see if the event has any subscribers (it's not null) and then invoke it: 
```
protected void RaiseMyEvent(int value) // the delegate we are matching
{ 
	MyEvent?.Invoke(value);
}
```

So here, `MyEventHandler` is similar to a function pointer: 
As a `delegate` they can be passed around, they are like function pointers in that sense. 
Making an event , we have the delegate type: 
```
public delegate void MyEventHandler(int value);
public event MyEventHandler myEvent;
```

Event based on the `MyEventHandler` delegate. 
Then we can add subscribers to our events: 
Other classes or objects can subscribe to events to listen for notifications:
```
public class Subscriber 
{ 
	public void SubscribToEvent(Publisher publisher)
	{ 
		publisher.MyEvent += OnMyEvent;
	}

	private void OnMyEvent(int value)
	{ 
		Console.WriteLine("Event Received with value: " + value);
	}
}
```
This will register `OnMyEvent` method as an event handler.

```
using System;

public class Publisher 
{ 
	// basically function pointer
	public delegate void MyEventHandler(int value);

	public event MyEventHandler MyEvent;

	public void TriggerEvent(int value)
	{ 
		MyEvent?.Invoke(value); // Raise the event if there are subscribers
	}
}

public class Subscriber
{ 
	public void Subscribe(Publisher publisher)
	{ 
		publisher.MyEvent += OnEventReceived;
	}

	public void OnEventreceived(int value)
	{ 
		Console.WriteLine("Received EVvent with value: " + value);
	}
}

public class Program{ 
	public static void Main() 
	{ 
		Publisher publisher = new Publisher();
		Subscriber subscriber = new Subscriber();

		subscriber.Subscribe(publisher);

		publisher.TriggerEvent(42); // will called on Event received as well
	}
}
```

### **Basics of Events and Delegates**

#### **1. Function Pointers**

In C++, function pointers are the fundamental mechanism for handling callbacks and event handling. A function pointer holds the address of a function and allows indirect calling of that function.

**Example:**

```
void callbackFunction(int x) {
    std::cout << "Callback called with value: " << x << std::endl;
}

void invokeCallback(void (*func)(int), int value) {
    func(value); // Calls the function pointed to by 'func'
}

int main() {
    invokeCallback(callbackFunction, 42);
    return 0;
}

```

#### **2. Delegates (C# Equivalent)**

In C#, a delegate is a type-safe function pointer that can point to methods with a matching signature. Delegates are often used to implement events and callbacks.

**Example:**

```
public delegate void MyDelegate(int x);

public void MyMethod(int x) {
    Console.WriteLine("Value: " + x);
}

public void CallDelegate(MyDelegate del, int value) {
    del(value); // Calls the delegate
}

public void Example() {
    MyDelegate del = MyMethod;
    CallDelegate(del, 42);
}

```

#### **3. Events**

In C#, events are built on top of delegates. An event is a special kind of delegate that allows subscribers to register their interest in notifications.

```
public event MyDelegate MyEvent;

public void TriggerEvent(int value) {
    MyEvent?.Invoke(value); // Notify all subscribers
}

public void Subscribe() {
    MyEvent += MyMethod; // Subscribe to the event
}

```
#### **4. Subscribing and Invoking Events**

When an event is invoked, all subscribed methods (event handlers) are called with the same arguments passed to the event. Subscribers must match the delegate type of the event.


C++ Equivalent: 
```
#include <iostream>
#include <vector>
#include <functional>

class Publisher {
public:
    using Callback = std::function<void(int)>;

    void subscribe(Callback cb) {
        callbacks.push_back(cb);
    }

    void trigger(int value) {
        for (const auto& cb : callbacks) {
            cb(value); // Call all subscribed callbacks
        }
    }

private:
    std::vector<Callback> callbacks;
};

void subscriber1(int x) {
    std::cout << "Subscriber 1 received: " << x << std::endl;
}

void subscriber2(int x) {
    std::cout << "Subscriber 2 received: " << x << std::endl;
}

int main() {
    Publisher pub;
    pub.subscribe(subscriber1);
    pub.subscribe(subscriber2);

    pub.trigger(42); // Both subscribers will be called
    return 0;
}
```

This makes things very easy indeed. 
