An iterable - Object capable of returning its members one at a time
Conceptually very similar to coroutines, lazy evaluation : suspend and resume

Iters : They are objects with `__iter__()` and `__next__()` methods: 
Generaters : Functions that use the `yield`, auto creating an iterator

```
def my_generator(): 
	yield 1
	yield 2
	yield 3


gen = my_generator() # function object
print(next(gen))    # 1
print(next(gen))    # 2
print(next(gen))    # 3
```

Coroutines are way more complicated than this in C++20 lol

### Lists Are Iterables - but only with `iter()`
They implement the `__iter__()` method. 

They are not iterators themselves - they return an iterator when we ask `iter()` on them

```
my_list = [1, 2, 3]

iterator = iter(my_list)

print(next(iterator)) # 1 
print(next(iterator)) # etc
print(next(iterator))
```

### Internally, Python does this
```
for item in my_list: 
	print(item)

it = iter(my_list)# calls my_list.__iter__()
while True: 
	try: 
		item = next(it) # calls it.__next__()
		print(item)
	except StopIteration: 
		break
```

### C++
Very simple when we see it like this, and wouldn't take longer in C++, now that we have: 
```
for(auto i : someType)
```

In order to get this to work, the `for` loop just looks at our `begin()` and `end()`. 

### Iterators
They are similar to C++, they work differently under the hood. 
Sequential access to elements without exposing the underlying data structure: 

```
class MyIterator: 
	def __init__(self, data): 
		self.data = data
		self.index = 0

	def __iter__(self): 
		return self

	def __next__(self): 
		if self.index >= len(self.data): 
			raise StopIteration # signalling end of iteration
		value = self.data[self.index]
		self.index += 1
		return value
```

Then in C++, we get something like this: 
```
class MyIterat
{ 
	int* ptr; 

public: 
	MyIterator(int* p) : ptr(p) {} // already getting a pointer 
	
	int& operator*() { return *ptr; } // Dereference
	MyIterator& operator++() { ptr++; return *this; } // move to the next using pointer arithmetic
	bool operator!=(const MyIterator& other) { return ptr != other.ptr; }
};
```

