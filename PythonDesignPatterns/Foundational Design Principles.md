#### Encapsulate What Varies Principle

Dealing with change. 

Isolate parts of your code that are most likely to change and encapsulate them. 

Shielding the rest of the code from elements that are subject to change. 

Allowing us to make changes to one part, without changing the others. 

Done with polymorphism, getters and setters. 

OOP -> Polymorphism, a single interface to represent different types. 

###### Getters and Setters
Obviously with Python there is the `property` technique. 
Converting tribute access into method calls seamlessly. 
`@property` will be for the `getter`. 

And then `@attribute_name.setter` that allows for the `setter` of course. 

Examples: 
First, let's do the base class `process_payment()` method as well. Here we are encapsulating what varies, this is where it is happening. This just seems very obvious, but is important to know that this is happening. 

```
class PaymentBase: 
	def __init__(self, amount : int): 
		self.amount : int = amount

	def process_payment(self): # just like pure virtual function
		pass
```

So then we have the subclasses, the children. `CreditCard` and `PayPal`, that will inherit `PaymentBase`, providing their own implementations of `process_payment`. 

```
class CreditCard(PaymentBase): 
	def process_payment(self): 
		msg = f"Credit card payment : {self.amount}"
		print(msg)

class PayPal(PaymentBase): 
	def process_payment(self); 
		msg = f"Paypal payment = {self.amount}"
		pritn(msg)
```

This is all very clear. 

Then using a `property`. 

Using the classic shapes example. 

```
class Circle: 
	def __init__(self, radius : int): 
		self._radius: int = radius # radius being private

	@property
	def radius(self): 
		return self._radius # the key here is that if we didn't use _radius

	@radius.setter
	def radius(self, value : int):  # adding some functionality to setting this attribute
		if value < 0: 
			raise ValueError("Radius cannot be negative!")
		self._radius = value # you can see that the _ is saving us here again
		# again we need the _
		# anytime that we use the attribute name in the getter or the setter
		# we are going to have to use the _attribute_name idea
```

Remember that python does not enforce any access rules, like it does in C++. 
`_` before a variable, this does imply that this variable is private, or at least protected. 

The idea here is that we are exposing this with the property. 
This is the pythonic way of making it read only, but still accessible clearly. One would assume that we are not going to set this variable.

In this case as well, if we didn't use `_radius`, then we would be calling the property `getter` again. The issue should emerge clearly. That we are recursively calling, ad infinitum. 

#### Favour Composition Over Inheritance
Heard this one before. 
Read O'Reilly C++ Software Design for a better understanding over this. 

The idea is that we have less coupling here. 

Remember the idea of higher and lower. 

Inheritance demonstrates an is-a relationship, where there is tight coupling between the parent and the child. 
This is harder to change, extend without adding to the hierarchy, which could be elongated unnecessarily. 

Composition is of course a has-a relationship. 
Building complex behaviour by combining objects. 
More modular and reusable. 

The book is talking about how we can change behaviour at run time. This is due to the fact that we can change the object that composes another. 
Say that we have a `Car` and we have a bunch of engines, we can pass any engine to the `Car` that will change the behaviour, when we come to use the engine. 

Changing an object higher up a hierarchy, is going to be very tough, as then we have to change the entire tree. 
Again, super obvious, however, there is a subtlety to it that the C++ Software Design book goes into. 

Ease of Maintenance too, pretty clear. 

In fact, the book actually uses the `Car` example here:

```
class Engine: 
	def start(self): 
		print("Engine started")


class Car: 
	def __init__(self): 
		self.engine = Engine() # Default creating of an engine

	def start(self): 
		self.engine.start()
		print("Car Started")
```


#### Program to Interfaces, Not Implementations
Again, another principle that the C++ Software Design book talks about as well. 

Interfaces do define contracts for classes, how we use them, and the specific methods that must be implemented, that will each have pre and post conditions. 

Interfaces can be implemented by using two primary techniques: **Abstract Base Classes** (same in C++) and **Protocols**. 

###### Abstract Base Classes
We do need a module here, due to Python's Zen, however, we can make it work. C++ doesn't require that, as we can just use Pure Virtual Functions. 

`fromt abc import ABC, abstractmethod`

Therefore: 
```
from abc import ABC, abstractmethod

class MyInterface(ABC): 
	@abstractmethod
	def do_something(self, param : str): 
		pass 

class MyClass(MyInterface): 
	def do_something(self, param : str): 
		pritn(f"Doing something with: '{param}'")
```

###### Protocols
Via the `typing` module. 
They offer a more flexible approach than `ABC`. 
`Structural duck Typing` - where an object is considered valid if it has certain attributes or methods, regardless of its actual inheritance. 

**Duck Typing**: If it quacks like a duck - the behaviour matters more than the actual typing here. 

**Traditional Duck Typing** (Python Style): We don't really care about the object, just that it behaves correctly. 
This is more afforded in python as the args in a function, can really be anything, as long as they behave in the way that  the implementation requires of them. 

```
def quack(obj): 
	obj.quack() # we don't check if obj is a duck, we just care that it quacks
```

Structural Typing (Formal duck typing): 
Stricter, more explicit: 
Not checking for an exact class, but we're checking for the structure. 
As the book says, that we do this from the `typing` module using `Protocols`. 

```
from typing import Protocol

class Quacks(Protocol): 
	def quack(self) -> None: ... 

def make_noise(obj : Quacks): 
	obj.quack() # typei checker will verify structure
```

C++ doesn't really do the formal duck typing (gives the idea that as long as it quacks, no matter what it is, it is considered a duck), as it is already strongly typed, there is some mimicking from templates: 
```
template <typename T>
void quakc(T& obj)
{ 
	obj.quack(); // there is no checking for the base class, as long as T has quack()
}
```
Concepts are very close to structural typing, as they give the idea of requiring certain aspects of the object passed in. 

```
template <typename T> 
concept Quacks = requires(T obj){ 
	{obj.quakc()} -> std::same_as<void>; 
}

template <Quacks T>
void make_nosie(T& obj)
{ 
	obj.quack(); // requiring some structure of the 
}
```

Back to the book. 

Say that we have a `Drawable` protocol ( a rule for the `obj` coming in ), that requires a `draw()` method. 

Any class that implements this method, would implicitly satisfy without explicitly inheriting from it. 

```
from typing import Protocol
class Flyer(Protocol): 
	def fly(self) -> None: 
		....
```
Now, any class that has a `fly()` method, would be considered a `Flyer`, whether it inherits or not, which is wild to think about. 

Let's say that we are wanting to use different types of `Logger`

```
from abc import ABC, abstractmethod

class Logger(ABC): 
	@abstractmethod
	def log(self, message : str): 
		pass 

class ConsoleLogger(Logger): 
		def log(self, message : str): 
			print(f"Console : {message}")

class FileLogger(Logger): 
	def log(self, message : str): 
		with open("log.txt", "a") as f: 
			f.write(f"File: {message}\n")

def log_message(logger : Logger, message : str): 
	logger.log(message)
```

Now something similar but using `protocols`

```
from typing import Protocol

class Logger(Protocol): 
	def log(self, message : str)
		... 

class ConsoleLogger:  # no inheritance, however, still considered a Logger due to protocol
	def log(self, message : str): 
		print(f"Console: {message}")

class FileLogger:  # no inheritance, however, still considered a Logger due to protocol
	def log(self, message : str): 
		with open("log.txt", "a") as f:   # context manager
			f.write(f"File: {message}\n")


def log_message(logger : Logger, message : str)
	logger.log(message)
```

#### Loose Coupling Principle
Don't want to tangle any relationships that we do have. 

Minimizing the dependencies between different parts of a program. 
Well-defined interfaces for interactions, so that we can change one part without affecting others too much. 

How do we achieve loose coupling: 
- **Dependency Injection** 
- **Observer Pattern** - this is the one that I have the most trouble with
Dependency injection - receive dependencies from an external source rather than creating them, making it easier to swap or mock these dependencies.
Allowing objects to subscribe to an object, so that any state change will be noticed. 

We are just looking at reducing the interdependencies between components. 
Also, looking for a more modular design that is easier to manage. 

```
class MessageService: 
	def __init__(self, sender): 
		self.sender = sender # sender being the dependency injection

	def send_message(self, message : str); 
		self.sender.send(message)

class EmailSender: 
	def send(self, message : str): 
		print(f"Sending email: {message}")

class SMSSender: 
	def send(self, message : str): 
		print(f"Sending SMS: {message}")l
```

See that there is the contract, that we have to use `send` from the Sender's interface

More on the observer pattern : 
We have a subject, the thing that is being watched. And observers, being that which is watching a subject. 

When the subject updates, it notifies all observers. 

[Observer in Python](https://refactoring.guru/design-patterns/observer/python/example)

Writing through some of the code. 

The key thing here is that the subject (aka observable) is responsible for: 
Managing the observers/subscribers - this could be done with dependency injection as well. 
Then it has to notify these when something about its state changes. 

```
from __futre__ import annotations
from abc import ABC, abstractmethod
from random import randrange
from typing import List

class Subject(ABC): 
	"""
	The Subject interface declares a set of methods for managing subscribers
	"""
	# so this is in the subject itself
	# the observer then notifies too

	@abstractmethod
	def attach(self, observer : Observer) -> None: 
		"""
		Attach an observer to the subject
		"""
		pass # producing a "pure virtual function" eg. virtual void virtFunc() = 0
		# they cannot have a body (as far as I'm aware)

	@abstractmethod
	def notify(self) -> None: 
		"""
		Notify all observers about an event
		"""
		pass

class ConcreteSubject(Subject): 
	"""
	The Subject owns some important state and notifies observers when the state changes.
	"""

	_state : int = None
	# very simple state, of course, this could be more convoluted
	
	_observers : List[Observer] = [] # A simple list of observers, ev
	"""
	List of subscribers. 
	This could be stored in a manner that deals varying types of observers, perhaps using a dict, i'm not sure, more research to follow
	"""

	def attach(self, observer : Observer) -> None 
		print("Subject: Attached an observer.")
		self._observers.append(observer)

	def detach(self, observer : Observer) -> None:
		self._observers.remove(observer)
	
	"""
	The subscription management methods - dealing with observers etc.
	"""

	def notify(self) -> None: 
		"""
		Trigger some form of update in each subscriber
		"""
		
		print("Subject: Notify observers...")
		for observer in self._observers: 
			observer.update(self) # passing this subject to the observers I guess, however, we could pass a form that presents the state of this subject


	def some_business_logic(self) -> None: 
		"""
		Usually, the subscription logic is only a fraction of what a Subject can really do. 
		Subjects commonly  hold some important business logic, that triggers a notification 
		method whenever something imporatnt is about to happen (or after it).
		"""
		print("\nSubject: I'm doing something important")
		self._state = rangrand(0, 10)

		print(f"Subject : my state just change to {self._state}")
		self.notify() # passes _state on, could use different methods depending on which attribute changes
```

```
class Observer(ABC): 
	"""
	The Observer interface declares the update method, used by subjects
	"""

	@abstractmethod
	def update(self, subject : Subject) -> None: 
		"""
		Receive update from subject.
		"""
		pass


"""
Concrete Observers react to the updates issued by the Subject they had been attached to.
"""

class ConcreteObserverA(Observer): 
	def update(self, subject : Subject) -> None: 
		if subject._state < 3: 
			print("ConcreteObserverA : Reacted to an event")

class ConcreteObserverB(Observer): 
	def update(self, subject : Subject) -> None: 
		if subject._state == 0 or subject._state >= 2: 
			print("ConcreteObserverB: Reacted to an event")
```



