Remember that in python, scope is defined by indented code, just like in C++ it's `{}`. 

There is no need to collect a value that is returned from a function. 

One thing to remember is that unlike C++, we do not write : 
```
if n == 1 or n == 0: 

if(n == 1 || n == 0): 
```

Instead we do this really cool thing: 
```
if n in (1, 0): 
```

Then factorials become this really cool idea: 
```
from functools import reduce
from operator import mul

def factorial(n): 
	return reduce(mul, range(1, n + 1), 1)
```
Very elegant and concise: but also shows that we have to learn about the the imports that python uses, which is not really my thing tbh. 
`range()` producing numbers from 1 to `n + 1`. 
`mul` is the function from the `operator`module, just performs multiplication. 
`mul(2, 3)` returns `6` seems really obvious now lol. 

`reduce()` will apply a binary function (`mul`) cumulatively to the items of the iterable. 
`1` the third arg is the initial value. 

Just remember this, I feel like this is going to come up a lot: 

**Iterable**: any python object that can return an iterator. 
Most built in container types `lists, tuples, sets, dictionaries, strings`> 
`__iter__()`

**Iterator:** object that represents a stream of data. 
Keeps track of current state during iteration, knows how to fetch the next item from the iterable. 
Must implement both `__iter__()` and `__next__()`. 

```
my_list = [1, 2, 3]
iterator = iter(my_list)
print(next(iterator))
```

```
class MyIterable: 
	def __init__(self, start, end): 
		self.start = start
		self.end = end
		
	def __ieter__(self): 
		# the iter method should return an iterator

class MyIterator: 
	def __init__(self, start, end): 
	self.current = start
	self.end = end

	def __iter__(self): 
		# this must be done, that is just returns itself
		return self

	def __next__(self): 
		if self.current > self.end: 
			raise StopIteration # End of Iteration
		else: 
			self.current += 1
			return self.current - 1

my_iterable = MyIterable(1, 5)
for value in my_iterable: 
	print(value) # Output 1, 2, 3,4 ,5
```

