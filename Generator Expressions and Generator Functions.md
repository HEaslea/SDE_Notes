Both **return the same thing** a **Generator Object**. 

Generator Functions will yield one item at a time. 

### Generator Expressions
Built using an inline expression, sometimes using `()`

`gen = (x * 2 for x in range(5))`

When we do something along the lines of: 
```
gen = (x * 2 for x in range(5))

print(gen) # <generator object <genexpr> at 0x0000017c34a4d490>
```

We get a generator object, as a `<genexpr>`. 

They are like lazy version of list comprehensions 

The expressions use the same as list comprehensions, using the round brackets, not square. 
They seem to create the same sequence of values that we would obtain from the list comprehension. 
HOWEVER - instead of creating the entire list and storing that in memory - this will `yield` that value one at a time. 
You can only iterator over a generator once, then it will be exhausted. 


### Generator Functions
Like normal functions, except that we give back results one at a time. 

Built using `def` and `yield`

Imagine counting to 1000000, and after you stopped, then eventually you would pick back up and start from where you left off, that's essentially what a generator function is : coroutine in C++ (stack less or stack full I can't remember). 

```
def get_square(x):  // normal function
	return [x**2 for x in range(n)]

def get_squares_gen(n): 
	for x in range(n): 
		yield x**2
		
print(list(get_squares_gen(5)))
```
Here I believe that the list constructor will exhaust this generator object, until a `StopIteration` is raised. 
So we might do, added to the one above: 

```
try: 
	while True: 
		print(next(get_squares_gen(5)))
except StopIteration as e: # We get the StopIteration as a value, due to the return statement
	print("Returned: ", e.value)
```


If there is a `return` statement in the generator function, that will raise a `StopIteration` with a value as well: 

```
def g(): 
	yield 1
	return 99 # raises StopIteration(99), so that 99 is lost if we get a list from this

gen = g() # get the generator object 
print(list(gen)) # or print(list(g())), as both give the same output. 
```
`returns 10` - this will give the `StopIteration` for us to do: 
```
except StopIteration as e: 
	print("Returned: ", e.value)
```

`gen` is `<generator object g at 0x000001fae8fc49e0>`


```
def get_squares_gen(n): 
	for x in range(n): 
		yield x**2

squares = get_squares_gen(4) # this creates a generator object
print(next(squares)) # i believe this just calls next for that generator prints 0
print(next(squres))  # print 1
print(next(squres))  # print 4
print(next(squres))  # print 9
# This next StopIteration
print(next(squres)) 
```

The point the book is trying to make is best summed up with the analogy that it gives : imagine that we want to work with some permutations, of a length `N` - then we will have `N!` permutations, if `N` is large enough, that is a huge number. We would not be able to get through all of that. If we wanted to make a list, then that list would be huge, and realistically, given a normal function, we would not be able to work with that `list`, as it would be too large, and take too long to compute. 
However, say that we only want to work with one at a time - or a few at a time. Then we can just yield. 
This is huge, huge idea. 

When a function returns that list of permutations (or anything large), it: 
It will have to find the memory before returning all of those. 
Obviously an issue when `N` is very large. 

Generator function : 
Yields one permutation at a time. 
Only stores what it needs for the current step. 
Is **lazy** : meaning you can start using values immediately. 
We can handle infinite sequences like this, which you obviously can't do in functions without breaking out of them. 

Here is the idea with a geometric sequence: 
```
def geometric_progression(a, q): 
	k = 0
	while True: 
		result = a * q**k
		if result <= 100000: 
			yield result
		else: 
			return # stops the generating 
		k += 1
```

`cubes_gen = (k**3 for k in range(10)) # creating a generator object`
`<class 'generator'>`

`list(cube_gen) # this will exhaust the generator object`

```
def adder(*n): # take ain any number of positional arguments
	return sum(n) # i think this is passing in a list

s1 = sum(map(adder, range(100), range(1, 101)))
s2 = sum(adder(*n) for n in zip(range(100), range(1, 101))) # creating pairs and then passing t adder
```

Similar 
```
cubes = [x**3 for x in range(10)] # making a list 
odd_cubes = filter(lambda cube : cube % 2, cubes) # if true, so odd, then put it in
odd_cubes2 = (cube for cube in cubes if cube % 2)
```
Again we prefer the generator syntax, just much more clear. 
```
<filter object at 0x0000024938176bf0>
<generator object <genexpr> at 0x0000024937f336b0>
```
These are the objects that we get, very different. 
They can both be for looped over, and they produce the same thing. 

Again, simplifying the syntax, very helpful when things get a little more complicated. 
```
N = 20
cubes1 = map(
	# the return is a tuple here 
	lambda n: (n, n**3),  # do this for every element in the iterable given by filter
	filter(lambda n : n % 3 == 0 or n % 5 = 0, range(N))# create the iterable from the iterable range(N)
)

cubes2 = ((n, n**3) for n in range(N) if n % 3 == 0 or n % 5 == 0) # so much easier on the eyes 
```

### Generator Objects
Both the functions and the expression return a generator object - same base type under the hood `<class 'generator'>`. 

However, they are built differently. 

```
def gen_func(): 
	yield 1
	
gen_expr = (x for x in range(1))

print(type(gen_func())) # <class 'generator'>
print(type(gen_expr))   # <class 'generator'>
```

Generator functions will have a named `co_name`

```
def g(): yield 1

gen_f = g()
gen_e = (x for x in [1])

print(gen_f.gi_code.co_name)  # 'g'
print(gen_e.gi_code.co_namel) # '<genexpr>'
```

#### Generator Object Methods
There are methods in order to control their behaviour : `send(), throw(), close()`. 

`send()` is easier to digest, `throw()` and `close()` are a little more advanced. 

The idea being that we can pass values into the generator function: 
```
def counter(start=0): 
	n = start
	while True: 
		yield n
		n += 1

c = counter() # getting the generator object=
print(next(c)) # prints 0
print(next(c)) # prints 1
print(next(c)) # prints 2
```

This will run forever, so if we want it to stop, then obviously `stop()` in order to get it gone. 

You could use a global `bool` and run a check in the g-function. 

`yield` doesn't just give out a value - it can take a value and then use it in the function: 
```
def counter(start=0): 
	n = start
	while True: 
		result = yield n # A, we pass out n, and take in with send
		print(type(result), result) # B
		if result == "Q":
			break
		n += 1

c = counter()
print(next(c)) # C, yield will bring us out, for send to put is back in
print(c.send("WOW!")) #D execution resumes due to a send 
print(next(c)) # E
print(c.send("Q")) # F, will raise a StopIteration
```

```
0
<class 'str'> WOW!
1
<class 'NoneType'> None
2
<class 'str'> Q
Traceback (most recent call last):
  File "C:\Users\easle\OneDrive\Desktop\ArmInterview\main.py", line 14, in <module>
    print(c.send("Q")) # F
          ^^^^^^^^^^^
StopIteration
```


## `yield from`
Given this:
```
def print_squares(start, end): 
		for n in range(start, end): 
			yield n ** 2


for n in print_squares(2, 5): # will exhaust the generator object
	print(n)
```

We can use a `yield from` expression to get the same result: 
```
def print_squares(start, end): 
	yield from (n**2 for n in range(start, end)) # a sub - iterator
```

Creating a sub-iterator like this, and then yielding from it in this manner - shorter, more readable. 
Easier to use in general. 


##### Why Is It Called List Comprehension
In maths, defining sets looks like: 
`S = { 2x | x ∈ ℕ, x < 5 }`

S contains all values 2x where x is a natural number < 5. 

How similar this looks to list comprehensions: 
`s = [2 * x for x in range(5)]`

Here we are constructing a list from an expression and a condition: 
Comprehension means - defining a list by describing its members in a compact, declarative way. 

#### Relationship between Generators and Iterators
How they differ too. 
Generators, you might think of a special kind of iterator that is created with `yield`. 

An iterator, remember, is a class with the `__iter__()` and `__next__()`. 

In order to get a generator, we have to use a function or an expression. 

Laziness - they do `yield` one item at a time - they are both lazy. 

**Iterators - we manage the state manually.** 
**Generators - python saves states between `yield`s**. 

Iterators are much more verbose, in the fact that we have to write out the class itself. 

**They both can be exhausted**, as in used up. 

Consider this `iterator` object: 
```
class Counter: 
	def __init__(self, limit): 
		self.i = 0
		self.limit = limit

	def __iter__(self): 
		return self

	def __next__(self): 
		if self.i < self.limit: 
			val = self.i 
			self.i += 1
			return val
		else: 
			raise StopIteration
```

This is something that you 100% could implement on your own, without having to do all this python `__` but it's nice that it's there for us to use with `for _ in <iter>`. 

`for x in Counter(3)`

Generators are like shortcuts for making iterators: 

```
def counter(limit): 
	i = 0
	while i < limit: 
		yield i
		i += 1
```


Using a generator is preferred in most cases: 
When you want to lazily iterate over a sequence.

We should be using iterators: 
We need multiple independent iterators over the same structure. 

There is more control over the internals. 

```
class MyRange: 
	def __init__(self, start, end): 
		self.current = start
		self.end = end

	def __iter__(self):
		return self

	def __next__(): 
		if self.current >= self.end: 
			raise Stopiteration
		val = self.current 
		self.current += 1
		return val
```

How a `for` loop works internally: 
`for item in iterable`: 
```
iterator = iter(iterable) #calls __iter__()

while True: 
	try: 
		item = next(iterator) # calls __next__()
	except StopIteration: 
		break;
	# do stuff
```
Get the iterator with `__iter__()` and then called `__next__()` until we can't anymore. 

As you can see `iterators` are the full package, the real deal, with more verbose internal state that we have full control over. 
`generators`  are very simple, python does everything of us, it's already an iterator. 

#### Spot the Difference
```
s1 = sum([n**2 for n in range(10**6)])
s2 = sum((n**2 for n in range(10**6)))
s3 = sum(n**2 for n in range(10**6))
```
They all produce the same results. 
`s2` and `s3` are equivalent, they are both generator expressions passed to the `sum()` function. `s2` has some redundant `()`. 

#### Readability and Performance
We can use any combination of `map()` `zip()` or `filter()`, or choose to go with comprehension or a generator. 

Remember that generators will save a lot of space. 

If we need to go over elements a few times, then generators are not the one, as we can only go over them once, before they are exhausted. 

`for` loops are regarded as consistently slower. 
The book is maintaining that `map()` and comprehensions run at C language speed within the interpreter, while a Python `for` loop is run as Python bytecode, within the Python VM, often much slower. 

### The Zen of Python
There is a charm about python. 
Go into any python environment and do `import this` and then run, it will print out the Zen of Python, the ideals that. 
```
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

Comprehensions and Generators are more implicit and explicit, can be quite difficult to read, and understand at a glance. 

Sometimes you have to break them apart inside out in order to understand fully. 

### Name Localization
Using names in comprehensions, the variable names in there, are local to that, they are not overriding the global variables. 

![[Pasted image 20250702025303.png]]

The weird thing is that after a `for` loop, if the variable was not defined before that, then it will be defined after it. 

```
s = 0 
for A in range(5): 
	s += A
print(A)
print(globals())
```
![[Pasted image 20250702025417.png]]

## Generation Behaviour in Built-ins
In Python 2, functions such as `map()`, `zip()` and `filter()`, returned `list`s  and not the iterable objects that they return now. 

The idea being that if we need a list here, we just use `list(<object>)`. 

As we have: 
```
r = range(5)
print(type(r)) # <class 'range'>
print(r) # range(0, 5) I believe this is the __str__()
print(list(r)) # [0, 1, 2, 3, 4], note the non-inclusive
```

Iterable returns are quite common, even with `open()`. 

Also true of `enumerate()`, dictionary `keys()`, `values()`, and `items()` methods. 

That's why you can do, `for k, c in items(<dictionary>):`. 

Python wants to reduce the memory footprint by avoiding wasting space wherever possible, as these functions and methods are used all the time, therefore, let's save a bunch of time by doing it this way. 

## One Last Example
From the book exclusively. 

Fibonacci : a great way of testing recursion, memoization techniques, and, in this case, whether they know about **generators** or not. 
Very rudimentary version of this.
```
def fibonacci(N): 
	"""Return all fibonacci numbers up to N"""
	result = [0]
	next_n = 1
	while next_n <= N: 
		result.append(next_n)
		next_n = sum(result[-2:])
	return result
```
Pretty basic, pretty easy to understand. 

Then we might say, that we just want to iterate over those numbers: 
```
def fibonacci(N): 
	"""Return all fibonacci numbers up to N"""
	yield 0 # that's the first
	if N == 0
		return # finish off here if N = 0
	a = 0
	b = 1
	while b <= N: 
		yield b
		a, b = b, a + b
```

Just quickly, the line `a, b = b, a + b`, **Python will always evaluate the right side, before the left side**. This means that the values are taken as before any assignments, therefore, the expression `a + b` will be evaluated with the values going into the expression, rather than than `a = b` which is first. 
**Right side evaluates first**. 

Due to `yield` this is now a generator function. 

In order to exhaust it, we have to use `list(fibonacci(n))`. 

Apparently we can make it even more elegant: 
```
def fibonacci(N): 
	a, b = 0, 1
	whie a <= N: 
		yield a
		a, b = b, a + b
```

