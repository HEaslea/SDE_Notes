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


