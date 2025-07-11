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
