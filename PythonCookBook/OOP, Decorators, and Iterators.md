## Decorators
```
from time import sleep, time

def f(): 
	sleep(0.3)

def g():
	sleep(0.5)

def measure(func): 
	t = time() 
	func()
	print(func.__name__, time() - t)

measure(f)
measure(g)
```

Then if we need to pass some args to the function. 

```
from time import sleep, time 

def f(sleep_time=0.1): 
	sleep(sleep_time)

def measure(func, *args, **kwargs): # fold in here 
	t = time() 
	funct(*args, **kwargs) # unfold that which we folded in earlier
	print(func.__name__, " took: ", time() - t)

measure(f, sleept_time=0.3)
measure(f, 0.2)
```

There is another improvement that we can do here. 

```
from time import sleep, time 

def f(sleep_time=0.1):
	sleep(sleep_time)

def measure(func): 
	def wrapper(*args, **kwargs): 
		t = time() 
		func(*args, **kwargs)
		print(func.__name__, " took: ", time() - t)
		
	return wrapper

f = measure(f) # decoration point

f(0.2) # f took <whatever time around 0.2>
f(sleep_time=0.3) # f took <whatever time around 0.3>
```

The magic here is in the `decoration point`. 
We reassign `f` to be what we get from `measure()`. 

Therefore, whenever we call `f()` we are really calling `wrapper()`. 
That `wrapper()` will change depending on what `func` we pass to `measure(<function>)`. 

The wrapper will just call whatever `func` it was given, and then does some. 

This is known as **decoration**. 
Therefore `measure()` is effective a **decorator**. 


This idea become so widely used, that python added a special syntax for it. 

You can read that in PEP 38. 

###### Single Decorator Case
```
def func(ar1, arg2, ...): # not valid syntax btw, just in case you were wondering what ... is
	pass 
	
func = decorator(func)

# is equivalent to the following: 
@decorator
def func(arg1, arg2, ...): 
	pass
```


Therefore, the idea is that a decorator is a function that wraps another function (or class) to extend or modify its behaviour - without changing the source code. 

[Trust Reddit to ELI5](https://www.reddit.com/r/Python/comments/2lrhp5/comment/clxgkce/)

###### Higher-Order Programming
This is where we treat functions as just more things to work with, meaning that we can have functions that take other functions as arguments (I'm assuming that this is like callbacks), and then return new functions. 

Imagine that we have this function: 
```
def add(x, y): 
	return x + y
```

Then something in our code base, requires that we just add 2 often - and not any other number. 
Then we might do something like this: 
```
def add2(x): 
	return add(x, 2) # return 
```

This is the idea of **Currying**. 

This is much like argument binding in C++, where we lock in certain arguments for a function. 

We can even make a `make_adder` function that will do this for us, with a specified number: 
```
def make_adder(number): 
	def _adder(x): 
		return add(x, number)
	return _adder
```

So we would say
```
add2 = make_adder(2)
add3 = make_adder(3)
```

**Decoarators** are just another example of higher-order programming. 
A **decorator** is a function which takes a function as an argument, and may take other arguments as welll, and returns a new function. 

Say we have a function, want to make a new function that does some extra stuff before or after. 
Say if there was a need to debug by printing out the arguments as a function was called with, then: 
```
def arguemnt_printer(func): 
	def _wrapper(*args, **kwargs): 
		print("Called with pos args: %s" %list(args))
		print(" And kw args : %s" % kwargs)
		return fun(*args, **kwargs) # actualy calling i think
	return _wrapper # then actually get the new function, that relies on the old
```
I believe this is a design pattern, see the big four book on the shelf. 

Then we might do: 
`add = argument_printer(add)`

Or we could do: 
```
@argument_printer
def add(x, y): 
	return x + y
```

Now, any call to `add` will print out it's arguments, given the code before. 
Therefore, we need to write `argument_printer` somewhere else, then it will take `add` as the `func` being passed to it. 

Therefore a very brief definition: 
**Functions that modify the behaviour of other functions or classes without changing their code, using the `@decorator` syntax**. 

Back to the book: 
```
def func(arg1, arg2, ...): 
	pass 
func = decorator(func)

# is equivalent
@decorator # decorator name, is just the function name
def func(arg1, arg2, ...):
	pass
```

##### Multiple Decorators
PLEASE Take Notes of the Order that we are applying these in.
```
def func(arg1, arg2, ...):
	pass 
func = deco1(deco2(func))

@deco1
@deco2
def func(arg1, arg2, ...):
	pass
```

The idea being here that we are decorating with `deco2` before `deco1` despite `deco1` being above. 
Therefore the one closes to the function, will go first, then we move up through the decorators. 

>The closer the decorator is to the function, the sooner it is applied.


### A Few Examples Before the Book Goes on a Tangent
```
def my_decorator(func): 
	def wrapper(): 
		print("Before the function")
		func()
		print("After the function")
	return wrapper

@my_decorator
def say_hello():
	print("hello!")
```

Essentially this is just saying that we call the decorator before `func`, by passing in `func`. 

When we say `say_hello()` we are essentially just running the wrapper. 

```
def log_args(func): 
	def wrapper(*args, **kwargs): 
		print(f"Arguments: {args}, {kwargs}")
		return func(*args, **kwargs)
	return wrapper

@log_args # log_args(greet())
def greet(name):
	print(f"Hi, {name}!")

greet("Alice")
```

```
def double_result(func):
	def wrapper(*args, **kwargs): # do something around the function provided
		result = func(*args, **kwargs)
		return result * 2
	return wrapper

@double_result
def add(x, y): 
	return x + y

print(add(3, 4)) # Output: 14
```

##### Back to the Book
```
def measure(func): 
	def measure(*args, **kwargs): 
		... 
	return wrapper
	
f = measure(f) # decoration point
print(f.__name__) # wrapper <- as we are getting wrapper from the function above
```

Let's say that we don't want to lose the original function's name and docstring when we decorate it, we don't want `wrapper`. 
The original attributes are lost, replaced with all those of `wrapper()`. 

There is an easy fix using `functools` if you needed it. 

```
from time import sleep, time
from functools import wraps # get the wraps

def measure(func):
	@wraps(func) # so now we are wrapping the wrapper, good lord
		t = time()
		func(*args, **kwargs)
		print(func.__name__, " took: ", time() - t)
	return wrapper

@measure
def f(sleep_time=0.1)
	"""I'm a cat and I love to sleep!"""
	sleep(sleep_time)

f(sleep_time=0.3) # f took 0.3...
print(f.__name__) # f
print(f.__doc__) # I'm a cat and I love to sleep!
```

Just to clarify: 
```
def logWrapper(func): 
	def wrapper():
		print("Wrapping start")
		func()
		print("After wrapping")
	return wrapper

@logWrapper
def sing(): 
	print("Singing")

sing() # will do the wrapper as well, so all the print functions above
print(f) # produces None
```

###### `*args, **kwargs`

The idea behind the wrappers of this: 
```
def demo(*args, **kwargs): 
	print("Positional: ", args) # this makes a list, and we print the list in []
	print("Keyword: ", kwargs)  # this makes a dict, and we print the dict in {}
```

```
def my_dec(func): 
	def wrapper(*args, **kwargs): 
		print("Before function call")
		result = func(*args, **kwargs) # call the original function
		print("After function call")
		return result
	return wrapper
```

When we do something like this: 
```
@myDecorator
def greet(name): 
	print(f"Hello, {name}!")
	
# Python will rewrite to this
def greet(name):
	print(f"Hello, {name}!")
	
greet = myDecorator(greet) # you can see we are just passing the function
```

When we do this: 
```
def myDecorator(func): 
	def wrapper(*args, **kwargs): 
		print("Before")
		result = func(*args, **kwargs) # call the real function
		print("After")
		return result
	return wrapper # returns the wrapper function itself
```

Here `wrapper` gets called instead of the original function. 

```
@myDec
def say(name):
	print(f"Hi, {name}")

say("Alice")

# Behind the scenes
say = myDec(say) # becomes: say = wrapper

# Therefore calling say("Alice") is really: 
wrapper("Alice")

# Then inside wrapper: 
func = say # (the original say function)
func("Alice") # =>  prints, "Hi, Alice"
```

Decorators just replace the original function with a new one (`wrapper`) that: 
- Accepts any args 
- Does extra stuff
- Calls the original function with the same args
- Returns the result

### The Best Way to Think About It
Decorators are wrapper factories. 
Takes a function, wraps it, returns the wrapped version. 
And then just remember that when we run that function, we are actually running the wrapper. 
The main thing is that it is adding behaviour before and after. 
One issue being that if we do `f.__name__` we get `wrapper` or whatever the wrapper function is called, unless we do `@wraps(func)` from `functools import wraps`. 

```
from time import time
from functools import wraps

def measure(func):
	@wraps(func)
	def wrapper(*args, **kwargs): 
		t = time()
		result = func(*args, **kwargs) # these can be empty if there aren't any
		pritn(func.__name__, " took: ", time() - t)
		return result
	return wrapper

def max_result(func): 
	@wraps(func)
	def wrapper(*args, **kwargs)
		result = func(*args, **kwargs) # so that which we get from wrapped func
		if result > 100: 
			print(f"Result is too big({result}). "
			"Max allowed is 100."
			)
		return result 
	return wrapper

@measure  # call this one second
@max_result # call this one first, the closer it is, the earlier it's applied
def cube(n): return n**3

print(cube(2))
print(cube(5)) # we will get the error message first
```

### A Decorator Factory
Some decorators can take arguments. 
This is generally used to produce another decorator, producing decorators, therefore, that's a decorator factory. 

```
def func(arg1, arg2, ...): 
	pass 

func = decoarg(arg_a, arg_b)(func)

# is equivalent to the following
@decoar(arg_a, arg_b)
def func(arg1, arg2, ...): 
	pass
```
This means that we call `decoarg` with the given arguments, then we get the wrapper returned from that, then we call that `func()` or `wrapper()` depending on how you view it. 

Let's go back to the `max_result()`. We want to improve it, so that we can decorate different functions with different thresholds. 
We don't want to have to write a whole decorator for every single threshold. 

```
from functools import wraps

def max_result(threshold): 
	def decorator(func): 
		@wraps(func)
		def wrapper(*args, **kwargs): # take in
			result = func(*args, **kwargs) # unwrap here
			if result > threshold: 
				print(
					f"Result is TOO big ({result})."
					f"Max allowed is {threshold}."
				)
			return result
		return wrapper
	return decorator

@max_result(75)
def cube(n):
	return n**3
```

Remember that decorating a function is the same as writing `func = decorator(argA, argB)(func)`. 
So here, we are decorating here as: `cube = max_result(75)(cube)`. 

Let's go through it. 
The general idea is just that the decorator is called first, around the main `func` that we pass in. 
When we call cube, we are really calling `max_result(75)`, then we enter that. 
Then we have defined a function called `decorator`. 
Then `wrapper()` will check to see if the result is too large or not. 

Remember that we can still use `threshold` in the inner functions. 
The `wrapper()` returns result. 
Then `decorator()` returns `wrapper()`, and `max_result()` returns `decorator()`. 
Meaning that `cube = max_result(75)(cube)`, that really becomes `cube = decorator(cube)`. 

In Python, a closure is: a function that remembers variables from its enclosing scope, even after that scope has finished. 

In C++, a closure is: an object created by a lambda expression that captures variables from its surrounding scope.

The idea is that we are capturing/closing a variable from the surrounding scope. 

```
def decorator(func): 
	def wrapper(): 
		print("Before")
		func() # using func from outer scope
	return wrapper
```
Here `wrapper()` closes over `func`. 

Dynamic functions that are returned by other functions are called closures. 
They have complete access to the variables around them at the time, even after the scope around them returns. 

Now we can set different thresholds depending on the function that we are decorating: 

```
@max_result(75)
def cube(n): 
	return n**3
	
@max_result(100)
def square(n):
	return n**2

@max_result(1000)
def multiply(a, b): 
	return a * b
```

## OOP
Remember that everything in Python is already an object. 

Objects are instances of classes of course. 

##### Simple Python Class: 
```
class Simplest: 
	pass
	
print(type(Simplest)) # <class 'type'>

simp = Simplest()             # <class 'type'>

print(type(simp))             # <class '__main__.Simplest'>
print(type(simp) is Simplest) # True
```

Classes, here, are in fact objects themselves. 
They are instances of type. 
This would take us into **metaclasses** and **metaprogramming**. 

We say that classes themselves are objects, created by the `type` metaclass. 

When we define a class, Python actually does: 
`MyClass = type('MyClass', (), {})`, which is wild. 

Then we have instances: 
`obj = MyClass()`
`print(type(obj)) # <class '__main__.MyClass'>` 

This is just to say that even classes are objects, and their type is `type`, which just sounds so weird, but we move. 

Come back to this at some point. 

### Class and Object Namespaces
Call the class to create instances. Each instance inherits the class attributes and methods and is given its own namespace. 

To walk a namespace we just have to use the `.` operator. 

```
class Person: 
	species = "Human"

print(Person.species) # Human
Person.alice = True   # Added dyanmically! THIS IS NEW
print(Person.alive)   # True

man = Person()
print(man.species)    # Human (inherited)
print(man.alive)      # True  (inherited) 

Person.alive = False
print(man.alive)      # False (inherited)

man.name = "Darth"    # I think we are adding dynamically adding here as well
man.surname = "Vader" # 

print(man.name, man.surname) # Darth Vader
```


The idea being that if we add to the class itself, then all the instances will inherit from it, unless they start to override it. 
If we only dynamically add to an instance, then only that object will. 
The `name` and `surname` here are considered **instance attributes**. 
Class attributes are shared among all instances, while instance attributes are not; 
Class attributes to provide the states and behaviours to be shared by all instances, and use instance attributes for data that will be specific to each individual object. 

### Attribute Shadowing
When you search for an attribute on an object and cannot find it, python will extend the search the attributes on the object's class, will keep searching until the attribute is either found or the end of the inheritance chain is reached. 

```
class Point:
	x = 10
	y = 7

p = Point()

print(p.x) # 10 (from class attribute)
print(p.y) # 7  (from class attribute)

p.x = 12        # p gets its own 'x' attribute
print(p.x)      # 12 (now found on the instance)
print(Point.x)  # 10 (class attribute is still the same)

del p.x    # we delete the instance attribute
print(p.x) # 10 (now search has to go to find the class attr)

p.z = 3    # now a make-shift 3D point
print(p.z) # 3

print(Point.z) # AttributeError : type object 'Point' has no attribute 'z'
```

Away from the book a little bit, so that I stick the landing here. 

Class attributes, belong to the class, the whole class, it is shared across all instances, **unless shadowed**. 

```
class Dog: 
	species = "Canine"

a = Dog()
b = Dog()

print(a.species) # Canine
print(b.species) # Canine

Dog.species = "Wolf"

print(a.species) # Wolf
print(b.species) # Wolf
```

Instance attributes, they belong to a specific instance/object. 
They are stored in the `obj.__dict__`: 
```
a.name = "Rex"

print(a.name)   # rex
#print(b.name)  # AttributeError : 'Dog' object has attribute 'name'
```

Python will look for the attributes in the following order: 
1. Instance's `__dict__`
2. Class's `__dict__`
3. Base Classes (in MRO order)

```
a = Dog()
a.species = "Wolf"

print(a.species)   # Wolf (instance attribute)
print(Dog.species) # Cnanine (class attribute still exists)
```

And we can dynamically add attribute: 
```
Dog.legs = 4      # class attribute, shared
a.tail = True     # Instance attribute, only on 'a'
```

Other instances will not see `a.tail` yet they will say `Dog.legs`
```
b = Dog()
print(b.legs)                 # 4
print(hasattr(b, 'tail'))     # False
```

We can inspect the internals: 
```
print(a.__dict__)    # {'species': 'Wolf', 'tail': True, 'legs': 3}
print(Dog.__dict__)  # contains 'species', 'legs', etc.
```

#### The `self` Argument
In a class method, we can refer to an instance by means of a special argument, `self`. 
`self` is always the first attribute of an instance method. 

Sharing methods will all instances: 
```
class Square: 
	side = 8
	
	def area(self): # self is a reference to the instance that called it
		return self.side**2 # this means it doesn't have to be 8. 

sq = Square()
print(sq.area())       # 64 (side is found on the class)
print(Square.area(sq)) # 64 (equavalent to sq.area)

sq.side = 10
print(sq.area()) # 100 (side is found on the instance)
```

The two calls, `Square.area(sq)` and `sq.area()` are equivalent. 

They give us an idea of how this works. 

You can do `Square.area(sq)` or `sq.area()`, and python will translate that for you behind the scenes. 

```
class Price: 
	def final_price(self, vat, discount = 0): 
		"""Returns the price applying vat and fixed discout."""
		return (self.net_price * (100 + vat) / 100) - discount
p1 = Price()

p1.net_price = 100

print(Price.final_price(p1, 20, 10)) # 110 (100 * 1.2 - 10)

print(p1.final_price(20, 10)) # equivalent
```

The first argument, despite any other arguments, will always be `self`. 

The idea is that we don't have to call it `self`, the idea is the same, that the first will always be the the object, due to the way that Python translates what we have written.

### Initializing an Instance
There are constructors in C++, in Python there are initializers. 
This is because it works on already created instance, it is called `__init__()`. 
A **magic method**, right after the object is created. 

Python objects have a `__new__()` method, which is the actual constructor. 
`__new__()` allocates and return a new instance. 
`__init__()` initializes the instance (after it's created). 

Apparently `__new__()` is a static method on the metaclass, I'm hoping this will become more apparent.

`__new__()` is responsible for creating the object. 
**Must return the instance of the class**. 
This is where we customize or control object creation. 

```
class A: 
	def __new__(cls, *args, **kwargs): 
		print("A.__new__ called")
		instance = super().__new__(cls) # yeah, no idea right now
		return instance

	def __init__(self, x): 
		print("A.__init__ called")
		self.x = x
		
a = A(10)
```

`__init__`, is an instance method, that is called after the object is created. 

This is used for setting up attributes, validation, etc. 
I'm sure this will become more apparent as we come up with more interesting objects. 

There are a few times when you use `__new__` and that is: 
- subclassing immutable types like `int, str, tuple`
- implementing a singleton, or cache instances
- change the instance's class ore turn a different object

```
class MyInt(int): 
	def __new__(cls, value): 
		print("MyInt.__new__")
		return super().__new__(cls, value)

	def __init__(self, value): 
		print("MyInt.__init__")
		self.info = "custom int"

i = MyInt(5)
print(i)  # 5
print(i.info)
```

Remember that `__new__()` will get `cls` as the instance does not exist yet, in order to do `self`

```
class Rectangle: 
	def __init__(self, side_a, side_b): 
		self.side_a = side_a
		self.side_b = side_b

	def area(self): 
		return self.side_a * self.side_b


r1 = Rectangle(10, 4)
print(r1.side_a, r1.side_b) # 10 4
print(r1.area()) # 40

r2 = Rectange(7, 3)
print(r2.area()) # 21
```

### OOP is about Code Reuse
##### Inheritance and Composition
We know this, from pattern designs, this is a core detail: 
**Inheritance**: this is a **Is-A** relationship. 
**Composition**: this is a **Has-A** relationship. 

Looking at inheritance first: 
```
class Engine: 
	def start(self): 
		pass 

	def stop(self): 
		pass

class ElectricEngine(Engine): # Is-A Engine
	pass

class V8Engine(Engine): # Is-A Engine
	pass
```

Then cars will **Have-A** Engine: 
```
class Car: 
	engine_cls = Engine # Has-A engine

	def __init__(self): 
		self.engine = self.engine_cls() # Has-A Engine

	def start(self): 
		print(
			f"Starting {self.engine.__class__.__name__} for "
			f"{self.__class__.__name__}... Wroom, wroom!"
		)
		self.engine.start()
		
	def stop(self):
		self.engine.stop()


class RaceCar(Car): # Inheritance, Is-A Car
	engine_clas = V8Engine

class CityCar(Car): # Inheritance, Is-A Car
	engine_cls = ElectricEngine

class F1Car(RaceCar): # Inheritance, Is-A RaceCar and also Is-A Car

car = Car()
raceCar = RaceCar()

cityCar = CityCar()

f1Car = F1Car()

cars = [car, racecar, cityCar, f1Car]


for car in cars: 
	car.start()
```

There is inheriting attributes and methods alike. 
`ElectricEngine`, `V8Engine` - where `Engine` here is the **base class** or the **parent class**. 

`Car` is a **base class**. 
`RaceCar` is also the **base class** of `F1Car`. 

There is a chain of inheriting here. 
`class A(B):` we say that `A` is a child of `B` and `B` is the parent of `A`. 

**Parent and Base are synonyms**, **Child of and derived from** are therefore the same as well. 

Something interesting here is that a **class is a subclass of itself**. 
A class is considered its own subclass. 
![[Pasted image 20250706230532.png]]


### Accessing a Base Class
When we do not specify a base class, the built-in **object** class is default the base class. 

Given this : `class A, class A(), and class A(object):` are all the same. 

How do we get to the base class from within. 

The `object` class hosts the methods that are common to all Python classes, does not allow you to sets any attributes on it. 

```
class Book: 
	def __init__(self, title, publichser, pages): 
		self.title = title
		self.publisher = publisher
		self.pages = pages

class Ebook(Book): 
	def __init__(self, title, publisher, pages, format): 
		self.title = title
		self.publisher = publisher
		self.pages = pages
		self.format = format
```

When writing this, you notice that 3/4 of the `__init__` of the subclass, is the same as the base class. 

Therefore, in order to reuse code: 
```
class Ebook(Book):
	def __init__(self, title, publisher, pages, format): 
		Book.__init__(self, title, publisher, pages)
		self.format = format
```
Take close look at the fact that we use `self` in pushing to the parent class. 

The issue with this, that you spotted is that we have hardcoded the name `Book` in `Ebook`, which means we will run into issues if we were to ever change `Book` into something like `Libre`. 

The better, more safe way of doing it is. 

```
class Ebook(Book): 
	def __init__(self, title, publisher, pages, format): 
		super().__init__(title, publisher, pages)
		# or we could do: 
		# super(Ebook, self).__init__(title, publisher, pages)
		self.format = format
```

Two classes are siblings if they share the same parent./ 

`super()` will return a proxy object that delegates method calls to a parent or sibling class, essentially just returns an object of the parent class. 

MRO - Method Resolution Order - The order in which classes are searched when executing a method or looking up an attribute, especially with multiple inheritance. 

Method Resolution Order:
It can actually be viewed: 
```
ClassName.__mro__ # or
ClassName.mro()
```

If we were given: 
```
class A: 
	def greet(self): 
		print("A")
		
class B(A): 
	def greet(self): 
		print("B")

class C(A): 
	def greet(self): 
		print("C")

class D(B, C): 
	pass

print(D.__mro__)

# (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
```

### Multiple Inheritance
When a class has more than one base class, attribute searching can follow more than one path. 

![[Pasted image 20250706233101.png]]

The deadly diamond, I've read it's not a great shape to code from and in. 

If a class doesn't have `__init__` defined in it, then it will just inherit from the base class, if no inheriting, then Python will create one for us. 


### Method Resolution Order
From the boo. 

We know that when we ask for `someObject.attribute` and `attribute` is not found on that object, Python will start searching in the class that `someObject` was created. 

With single inheritance, the MRO is just going all the way up to the `object` class. 

This can be an issue when we have multiple inheritance. 

It uses a C3 Linearization Algorithm - the lookup is deterministic and consistent, you know, that due to the consistency, which attribute will be found first. 

Python will look left to right in the inheritance list `D(B, C)`, so it will find the attribute in `B` first. 

### Class and Static Methods
#### Static Methods
When we create an object, Python assigns a name to it. 
That acts as a namespace, then it makes sense to group functionalities under it. 
Static methods, do not need to be called with an instance attached to it. 

Remember in C++: 
```
class MyClass{ 
public: 
	static void sayHello()
	{ 
		... 
	}
};


MyClass::sayHello(); // no object needed
```
It's roughly the same in Python: 

```
class StringUtil: 
	@staticmethod
	def is_plaindrome(s, case_insensitive=True): 
		# we allow only letters and numbers
		s = "".join(c for c in s if c.isalnum()) 
		# join exhausts generation expression here
		if case_insensitive: 
			s = s.lower()
		for c in range(len(s) // 2): 
			if s[c] != s[-c - 1]: 
				return False
		return True


	@staticmethod
	def get_unique_words(sentence): 
		return set(sentence.split()) # i really like this
```

Then we call using: 
```
print(
	StringUtil.is_palindrome("Radar", case_insensitive=False)
)
```

### Class Methods
These accept, as the first argument, the class object itself, rather than the instance. 

The main reason for this is for factory capabilities. 

```
class Point: 
	def __init__(self, x, y): 
		self.x = x
		self.y = y

	@classmethod
	def from_tuple(cls, coord): # cls is Point, which is teh class itself
		return cls(*coord)

	@classmethod
	def from_point(cls, point): # cls is Point
		return cls(point.x, point.y)
```
Here we have a little factor for the `Point` class. 

`cls` refers to the `Point` class, and this is something that most professionals use and would not change `cls`. 

`staticmethods` take no special first arguments (`self` or `cls`). 
These cannot access or modify class or instance state: logically related to the class, don't need access to the instance or class. 

```
class Math: 
	@staticmethod
	def add(a, b): 
		return a + b
		
print(Math.add(2, 3)) # 5
```

`classmethods` do have access to class state. 
These seem to be used when we are designing alternative constructors. 

```
class Person: 
	def __init__(self, name, age): 
		self.name = name 
		self.age = age 

	@classmethod
	def from_birth_year(cls, name, birth_year): 
		from datetime import date
		return cls(name, date.today().year - birth_year)

p = Person.from_birth_year("Alice", 2000)
```

```
class Demo: 
	def regular(self): print(f"regular: {self}")

	@classmethod
	def class_m(cls) print(f"classmethod: {cls}")

	@staticmethod
	def static_m(): print("staticmethod: no self or cls")


d = Demo()

d.regular() # instance

d.class_m() # class

d.static_m() # nothing passed implicitly
```

`cls` is used instead of hardcoding the class. 

`@classmethod` -  bound to the class, not an instance. 

They receive the actual class (`cls`) as their first argument and they have access to the class state. 

```
class Animal: 
	@classmethod 
	def make(cls): 
		return cls()

class Dog(Animal): pass 

a = Animal.make() # cls is Animal -> returns Animal()
d = Dog.make()    # cls is Dog    -> returns Dog()
```
If we had hardcoded `Animal()` instead of `cls`, then we would always return `Animal`, even if called from `Dog`. 

That's why it's so good when used in factories. 

Remember that class methods respect polymorphism. 
They dynamically receive the class that was used to call them, even if the method is defined higher in the inheritance tree. 
This is very similar to dynamic dispatch in C++. 

```
class Animal: 
	@classmethod: 
	def who_am_i(cls): # remember this for class methods
		print(f"I am {cls.__name__}")

class Dog(Animal): pass
class Cat(Animal): pass


Dog.who_am_i() # I am Dog
Cat.who_am_i() # I am Cat
```

They are like if there was a **virtual static function**, although this doesn't exist. 

That's the thing to remember is that we are passing in the class that called it, not an instance. 

Statics, don't pass either. 


```
class StringUtil: 
	@classmethod
	def is_palindrome(cls, s, case_sensitive=True): 
		s = cls._strip_string(s)
		# for case insensitive comparison, we loser-case s
		if case_insensitive: 
			s = s.lower()
		return cls._is_palindrome(s)

	@staticmethod
	def _strip_string(s): 
		return "".join(c for c in s if c.isalnum())

	@staticmethod
	def _is_plaindrome(s): 
		for c in range(len(s) // 2): 
			if s[c] != s[-c - 1];
				return False
		return True

	@staticmethod
	def get_unique_words(sentence): 
		return set(sentence.split())
```

```
print(StringUtil.is_palindrome("radar"))
print(StringUtil.is_palindrome("not a palindrome"))
```

Note that we are calling `is_palindrome` that same way that we did before. 
We have made it a `@classmethod` so that we can call using `cls._strip_string(s)`. Otherwise we have to hard code that class that we want to call from. 
This means that we have to change that code every time we change something, that is what we have to code against every step of the way. 

### Private Methods and Name Mangling
C++, obviously we have a `private` idea. 

In Python, there is no such thing. Everything is public. 
We rely on conventions, for privacy, on a mechanism called **name mangling**. 

The convention is as follows, and this was something that was really really confusing when we did our exams. 

**When the name has one leading underscore, the attribute is considered private**, meaning that it was intended to be used privately. 

This is common in private helper methods, that are used in public ones. 

There is an idea of call chains here. 

Remember, that Python has no concept of `const`, which seems really odd. 

This comes down discipline more than anything else. 
The book is saying that barely ever, do people run into bugs due to not having private attributes. 

This is funny because, then, why such a thing in C++. It makes total sense, would it just rely on discipline? 

There is a lot of freedom given to us in Python : apparently it is known as **the language for adults**. 

There are pros and cons to every design choice. 

Some people prefer languages that allow more power and might require a bit more responsibility, others prefer those that are more restrictive. 

```
class A: 
	def __init__(self, factor): 
		self._factor = factor # I believe this means it's private

	def op1(self): 
		print("Op1 with factor {} ... ".format(self._factor))


class B(A): 
	def op2(self, factor): 
		self._factor = factor
		print("Op2 with factor {}...".format(self._factor))

obj = B(100)
obj.op1()      # Op1 with factor 100...
obj.op2(42)    # Op2 with factor 42...
obj.op1()      # Op1 with factor 42... <- THIS IS BAD
```

Let's say that we want to keep this constant, we don't want it to change with `obj.op2(42)`
Fixing it with adding another leading underscore.

```
class A: 
	def __init__(self, factor): 
		self.__factor = factor

	def op1(self): 
		print("Op1 with factor {}...".format(self.__factor))

class B(A): 
	def o2(self, factor): 
		self.__factor = factor
		print("Op2 with factor {}...".foatmr(self.__factor))

obj = B(100)
obj.op1()    # Op1 with factor 100...
obj.op2(42)  # Op2 with factor 42... 
obj.op1()    # Op1 with factor 100... <- Now it's good
```

The **name mangling** mechanism is kicking in here. 

The idea is that if we have 2 leading underscores, and at most one trailing underscore, such as `__my_attr` is replaced with a name that includes an underscore and the class name before the actual name, such as `_ClassName__my_attr`. 

This means that when we inherit from a class, the mangling mechanism gives your private attribute two different names, in the base class and the child classes so that name collisions is avoided. 

We should be able to see this in action using the idea of `obj.__dict__`. 
`print(obj.__dict__.keys()) # get the attributes/and methods I think from this obj's class`. 
`# dict_keys(['_factor'])`
This is from the problematic version. 

```
print(obj.__dict__.keys())
# dict_keys(['_A__factor', "_B__factor'])
```
You can see how the two names are different per class.

So the `__`, 2 underscores, means that attribute will be given a name that is characteristic to the class 

They are two different attributes when we have the 2 underscores. 

The overall thing to take away : 
`_ClassName__attributeName` - that is what is created from `__attributeName` with the 2 underscores before it. 

```
class Test: 
	def __init__(self): 
		self.__secret = 42

	def reveal(self): 
		return self.__secret

Internally __secret becomes: 
self._Test__secret
```

This will prevent name classes between base and subclasses.
```
class Base: 
	def __init__(self): 
		self.__value = 10

class Sub(Base): 
	def __init__(self): 
		super().__init__()
		self.__value = 99 # this becomes _Sub__value, doesn't override the base
```


My own example: 
```
class Base:
    def __init__(self):
        self.__type = "Base"
    def printOut(self):
        print(f"My Type is {self.__type}")

class Sub(Base):
    def __init__(self):
        self.__type = "Sub"

    def printOut(self):
        print(f"My Type is {self.__type}")

b = Base()
s = Sub()

b.printOut()
s.printOut()
```

`__name` means that we don't want this to be inherited or clashed with in subclasses. 
`__type` This should be local to the class - don't let subclasses touch it. 

If we are going to be using classes that are mean to be used and extended by other developers, you will need to keep this in mind in order to avoid the unintentional overriding of your attributes. 

### The Property Decorator
pg 239