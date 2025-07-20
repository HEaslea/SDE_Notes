**Exceptions** and **Context Managers**: 

### Exceptions
There are lots of errors: 
`IndexErrors` , or `AttributeError`, or even `KeyError`. 

They are not lethal. `StopIteration` is one that is deeply ingrained. 

**exceptions** : they give us the opportunity to fix something, in response. They let us know there is an issue, and what the issue is, and then give us the opportunity to fix it. 
Say if the user tires to open a corrupted file, we need to have the ability to fix that. 

Dealing with exceptions means that we are given a chance to deal with a problem, without the program crashing out. 

#### Raising Exceptions
Just use the `raise` statement, with an Error behind it. 
`raise NotImplementedError`

**What are errors?** - they are objects, just like everything else in Python. 

They all inherit from `BaseException`. 

`raise ValueError("Bad input")` - where `raise` is just the same as `throw` in C++. 

#### Defining Your Own Exceptions
Defining your own, this is common when we write our own libraries. 

**All we have to do is define and inherit from another exception class**. 

All derive from `BaseException` :  this class is not intended to be directly subclassed. 
Our customs should be inheriting from `Exception` instead. 

### Tracebacks
This is a record of the call at the pointer where an exception occurred. 
- The sequence of function calls leading to the error. 
- The file names, line numbers, and code that was executing. 


```
def squareroot(number): 
	if number < 0: 
		raise valueError("No negative numbers please")
	return number**.5

def quadratic(a, b, c): 
	d = b**2 - 4 * a * c
	return (
		(-b - squareroot(d)) / (2 * a),
		(-b + squareroot(d)) / (2 * a),
	)
quadratic(1, 0, 1) 
```


![[Pasted image 20250710141313.png]]

The easiest way to do this, is to read from bottom to top. 

This was when we gave the function `0`. 

Following this Traceback allows us to see where the error landed. 

### Handling Exceptions
In order to handle, we use `try`, one this `try` block, Python will look for one or more types of exceptions.
If exceptions are raised, it allows you to react. 

Then we have the `except`, which is the same as the `catch` clause in C++. 

Then we also have the `else`: which runs if there are no exceptions thrown. 

`finally`, which will always happen, regardless of what goes on above it

This `finally` is used when we have to clean up resources. 

`except` and `else` can be omitted. 

The idea being that we have `try, except, else, finally`. 

It might look something like this: 
```
try: 
	# code that might raise an exception
	risky_operation()
	
except SomeException: 
	# will run if there is an exception thrown
	handle_error()

else: 
	# this runs if no exception occurred
	run_if_no_error()

finally: 
	# this run no matter what (error or not)
	cleanup() # as this is usually used in clean up. 
```

Think about it like this: try some risky code, then run some if there are errors thrown, run code if there aren't any exceptions, then run some code no matter what happens. 

```
def try_syntax(numerator, denominator): 
	try: 
		print(f"IN the try block: {numerator}/{denominator}")
		result = numerator / denominator
	except ZeroDivisionError as zde: 
		print(zde)
	else: 
		print("The result is: ", result)
		return result
	finally: 
		print("Exiting")
```

We can catch a couple of errors at one point: 
`except (ZeroDivisionError, TypeError) as e:`

Or we can separate them out, if we have to do different things per exception: 
```
try: 
	q, r = divmode(*values)
except ZeroDivisionError: 
	print("...")
except TypeError as e: 
	print(e)
```

 They will go in order, and only do the first exception that is caught, therefore, put the most specific at the top, and the most generic at the bottom, so that the generic doesn't catch and mess up the rest of them. 
 
We can do a catch-all, by using `BaseException`. 

You can also raise exceptions from within an `except` clause. 

Creating custom exceptions is very common, especially if you are writing your own libs. 

A very minimal example from stack overflow, where we just teal code and pretend :):
```
class SomeError(Exception): 
	def __init__(self, message, errors): 
		# call the base class constructor with the parameters it needs
		super().__init__(message)

		# custom code
		self.errors = errors
```

Even something more simple: 
```
class MyException(Exception): 
	pass

raise MyException("")
```

Raises will work similar to `throw` in C++. 
As in we go through the functions: 
```
def low_level(): 
	raise ValueError("Something went wrong")

def mid_level(): 
	low_level() # no try/except here

def high_level(): 
	try: 
		mid_level()
	except ValueError as e: 
		print("Caught exception:", e)
```

**Some core points**: 
- Keep the `try` clause as small as possible, just to the code that might be risky. 
- Make the `except` blocks as specific as you can at the top, then as general as you can as you go down. If you do too general, you have to imagine that you are going to capture exceptions that you did not actually intend to catch. 

### Not only for Errors

```
n = 100
found = False
for a in range(n): 
	if found: 
		break
	for b in range(n):
		if found: 
			break
		for c in range(n): 
			if 42 * a + 17 * b + c == 5096: 
				found = True
				print(a, b, c) # 79 99 95
				break
```

Here, we have to use `found` in order to break out of the three loops. 

Here is the alternative approach: 
```
class ExitLoopException(Exception): 
	pass


try: 
	n = 100
	for a in range(n):
		for b in range(n):
			for c in range(n):
				if 42 * a + 17 * b + c == 5096: 
					raise ExitLoopException(a, b, c)
except ExitLoopException as ele: 
	print(ele.args) # 79 99 95
```

Notice the use of the `args` here in order to get the numbers. 

### Context Managers
When working with external resources, we sometimes need some cleanup steps, when we are done. 
Say, after writing to a file, we need to close the file. 
Failing to clean up properly can sometimes result in a manner of bugs. 

In order to make sure this happens when an exception occurs, we might have to use `try/finally`, this is not always convenient, and could result in a lot of repetition, this is not always as obvious, especially if we are using a lot of the same resources over and over again. 

They create an execution context in which we can work with a resource and automatically perform any necessary cleanup when we leave that context, even if an exception was raised. 

Another use case, is to make temporary changes to the global state of our program. 
Say we want to change the level of precision, of an app just for a moment, but the default needs to stay the same. 

```
from decimal import Context, Decimal, getcontexxt, setcontext

one = Decimal("1")
three = Decimal("3")

orig_ctx = getcontext()
ctx = Context(prec=5)

setcontext(ctx)

print(f"{ctx}\n")
print("Custom decimal context: ", one / three)

setcontext(orig_ctx)
print("Original decimal context: ", one / three)
```


```
Context(prec=5, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999, capitals=1, clamp=0, flags=[], traps=[InvalidOperation, DivisionByZero, Overflow])

Custom decimal context:  0.33333
Original Decimal Context 0.3333333333333333333333333333
```

This is a common example : 
```
with open("data.txt", "r") as f: 
	content = f.read()
```

Is the same as: 
```
f = open("data.txt", "r")
try: 
	content = f.read()
finally: 
	f.close()
```
Again, some syntax and python will write more for us. 

Writing something like this: 
```
myFile = open("some/path")
try: 
	doStuff(myFile)
finally: 
	myFile.close()
```

Remembering to do this all the time, a bit bothersome, hence, we have the `with` clause: 
```
with open("some/path") as myFile: 
	doStuff(myFile)
```

And Python will do all the work, as per usual: 

[Context Manager, Some Comments](https://www.reddit.com/r/learnprogramming/comments/1fv0nxv/what_is_exactly_a_context_manager_python/)

```
class MyContextManager: 
	def __enter__(self): 
		print("With got called...")
		return "This is what's put in the 'as whatever' Value"


	def __exit(self, exc_type, exc_value, exc_tb): 
		print("Leaving the context...")

with MyContextManager(): 
	print("Inside the context manager")
```

```
class TestContextManager: 
    def __enter__(self):   # only self coming in here
        print("__ENTER__ of CM")
        return "Return Statement of __ENTER__"

    def __exit__(self, exc_type, exc_value, exc_tb): 
        print("__EXIT__ of CM")
    

with TestContextManager(): 
    print("Inside the CM now")
```

```
__ENTER__ of CM
Inside the CM now
__EXIT__ of CM   
```

```
with thing as x: 
	do_something(x)
```


```
class FileContext: 
	def __init__(self, path): 
		self.file = open(path, 'r')

	def __enter__(self):
		return self.file # this becomes as 'f'

	def __exit__(self, exc_type, exc_val, exc_tb): 
		self.file.close()
```

`with FileContext("data.txt") as f: f.read()` 

It looks so simple. 

Really, anything that implements those two methods is a context manager. 

`def __enter__(self)` and `def __exit__(self, exc_type, exc_val, exc_tb)`. 

With the code from the book, where it's looking at `decimal.Context`. 

In this sense, it really just is an object that remembers and stores settings, that will change how operations behave. 

These contexts, from these objects, we can `print(context_obj)` and we get the settings that we passed into the objects. 


```
from decimal import Context, Decimal, getcontext, setcontext

one = Decimal("1")
three = Decimal("3")

orig_ctx = getcontext()
ctx = Context(prec=5)
setcontext(ctx)
try: 
	print("Custom decimal context:", one / three)
finally: 
	setcontext(orig_ctx)
print("Original context restored: ", one / three)
```

SEE HERE, what did we just talk about `with`. 

This code is much safer, much much safer. 

This also violates the **DRY** principle. 

Instead, we might just use the `localcontext` rather than `set` and `get` contexts manually. 

```
from decimal import Context, Decimal, localcontext

one = Decimal("1")
three = Decimal("3")

with localcontext(Context(prec=5)) as ctx: 
	print("Custom decimal context", one /  three)
print("Original context restored:", one / three)
```

Basically the same code, however, we don't have to set and get manually. 

`with` - used to enter a runtime context, defined by `localcontext`. Then when we leave, we use the exiting code block. 

We can even combine multiple context managers in one `with` statement. 

```
from decimal import Context, Decimal, localcontext

one = Decimal("1")
three = Decimal("3")

with(
	localcontext(Context(prec=5)), # setting the precision part of the Context
	open("Output.txt", "w") as out_file # see that we can do two at the same time
): 
	out_file.write(f"{one} / {three} = {one /three }\n")
```

The same thing goes for both, `__enter__` and `__exit__`. 

There are of course, a huge range of context objects. 

- Socket objects, which implement low level networking interface, used as context manager to automatically close network connections. 
- The lock classes used for synchronization, in concurrent programming use the context manager protocol to automatically release locks. 

#### Class-based Context Managers
There are the two magic methods, that we have talked about: `__enter__()`and `__exit__()`. 

```
class MyContextManager: 
	def __init__(self): 
		print("MyContextManager init", id(self))
		
	def __enter__(self): 
		print("Entering 'with' context")
		return self;

	def __exit__(self, exc_type, exc_val, exc_tb): 
		print(f"{exc_type=}, {exc_val=} {exc_tb=}")
		print("Exiting 'with' context")
		return True # I'm not convinced that you have to do this

with MCM() as mgr: 
	print("Inside context")
    print(id(mgr))
    raise Exception("Exception inside 'with' context")
    print("This will never be reached")

print("After the 'with' context")
```

```
MCM Init
Entering __ENTER__ with 'with'
Inside context
2346262556752
 Type: <class 'Exception'>, Value : Exception inside 'with' context, TB: <traceback object at 0x00000222482860c0>
Exiting 'with' context
After the 'with' context
```

We can see that `tb` is the traceback object. 

### Generator-Based Context Managers
Sometimes there is no class that we would like to have this context manager approach with.
The `contextmanager` decorator, from `contextlib`, so that we do not have to write an entire class in order to use the context manager behaviour. 

This is a decorator. 

This will take a **generator function**, remember using `yield` and converts it to a context manager. 
Here, the decorator, which is the context manager `@contextmanager`, will enable the `__enter__()`, which starts the generator, and returns whatever the generator yields. 
If an exception occurs within the `with` statement, the `__exit__()` method passes the exception into the generator. 
Otherwise, `__exit__()` calls `next` on the generator. 
Oddly, there should only be one yield. 

```
from contextlib import contextmanager

@contextmanager
def MCM(): 
	print("Entering 'with' context")
	val = object()
	print(id(val))
	try: 
		yield val
	except Exception as e: 
		print(f"{type(e)=} {e=} {e.__traceback__=}")
	finally: 
		print("Exiting 'with' context")

print("About to enter 'with' context")
with MCM() as val: 
	print("Inside 'with' context")
	print(id(val))
	raise Exception("Exception inside 'with' context")
	print("This line will never be reached")
print("After 'with' context")
```


Remember, that when we have `{someValue=}` , this is saying that we print the expression, and show it's value, like a miniDebug. `print(f"{name=}")` might look something like: `name='Alice'`. 

This is the output:
```
About to enter 'with' context
Entering 'with' context
2514229363424
Inside 'with' context
2514229363424
type(e)=<class 'Exception'> e=Exception("Exception inside 'with' context") e.__traceback__=<traceback object at 0x0000024963e07a00>
Exiting 'with' context
After 'with' context
```

Essentially the idea is that we use `with` blocks with some generator function, so that we don't have to write an entire class in order to get this `with` block to work. 

A more general look at it, is like this: 

```
from contextlib import contextmanager

@contextmanager 
def open_file(path): 
	f = open(path, 'r')
	try: 
		yield f # Control returns to the with-block here
	finally: 
		f.close() # This runs when the with-block exits

with open_file('data.txt') as file: 
	contents = file.read()
```

In reality, the usage is when we don't want to write as much boilerplate. 
Cleaner and more intuitive, to some degree. 

Great for setup/teardown logic like logging timing, file handling, DB Conntection etc. 

```
from contextlib import contextmanager

@contextmanager
def testingContext(): 
	print("Star of testingContext")
	try: 
		yield "The yield statement" # return back to the 'with' with this statement
	finally: 
		print("Cleaning up: End of the function")


print("Before running the with block")
with testingContext() as tC: 
	print(tC) # getting the yielded statement back
	print("Inside the 'with' context)
	# Calling the finally part
print("After the 'with' block")
```

### Thinking Clearly
`with` defines a block: 
The Context Manager is the object returned by the expression after `with` - it **must** have defined `__enter__` and `__exit__`. 
Python will call those automatically at the beginning and the end of the block, after the initialization. 
![[Pasted image 20250715153608.png]]

You can see the order here, we init, then we call `__enter__()` and then we go inside, 
