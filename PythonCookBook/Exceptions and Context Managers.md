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


