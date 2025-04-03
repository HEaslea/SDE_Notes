[[Python Iterables]]

#### Difference Between Iterable and Iterator
`Iterables` need `__iter__()` that returns an `iterator` - `iterables` can be iterated over, but does not do the iterating. These might be `lists, strings, tuples, sets, dicts`.
`Iterator` - does the actual iterating, has the `__next__()` method

`iterable` example: 
```
numbers = [1, 2, 3] #list is an iterable
print(hasattr(numbers, "__iter__")) # True
```
```
# getting the iterator
numbers = [1, 2, 3]
iterator = iter(numbers) # get the iterator 

print(next(iterator)) # 1 etc. .. 
```

`iterator(s)` will have an `__iter__()` however, it will just return itself. 



Everything is an object in python, meaning that every variable, function, class, module, even built in, is a data type, is an instance of a class. Only supporting object orientated is the thing. 

This is not true in C++, C++ supports object orientated, however, it's still a multi-paradigm language, both objects and non objects. Primitive Types are not objects in C++: Functions are not inherently objects, however, we can utilise functor objects. References are not. 
### What is Itertools
Module that provides, fast, memory-efficient tools for working with iterators (objects that produce one value at a time). 

##### Infinite Iterators
`itertools.count(start=0, step=1)` this will just go on forever, until we break out of a loop. 
```
from itertools import count

for num in count(10, 2): 
	print(num)
	if num > 20: #something like this is necessary
		break
# 10 12 14 16 ... -> so on, will print 22
```
`itertools.cycle(iterable)` `Iterable` -> this is an object that can be looped over (requiring `__iter__()`)
```
from itertools import cycle
colors = ["red", "blue", "green"]
cycle = cycle(colors) # produces an iterator it seems

for _ in range(5): 
	print(next(cycled))
```

`itertools.repeat(element, times=none)`
repeats value indefinitely or `times` times.
```
from itertools improt repeat

for num in repeat(5, 3):  # repeat 5, 3 times
	... 
```

#####  Combinatorics Functions
Permutations, Combinations and products. 
Remember that permutations is when the order doesn't matter

`itertools.permutations(iterable, r=None)` gets an iterator based on the iterable.
```
from itertools import permutations

perm = list(permutations("ABC", 2))
print(perm) # permuations of length 2
```

`itertools.combinations(iterable, r)`
**unordered**, r length selections (remember that combinations, order does matter)
```
from itertools import combinations

comb = list(combinations("ABC", 2))
#[('A', 'B'), ('A', 'C'), ('B', 'C')]
```

`combinations_with_replacements` the same but you can have repeats in the combinations. 

`itertools.product(*iterables, repeat=1)`
The cartesian product of two sets: 
```
prod = list(product("AB", "CD")
#`[('A', 'C'), ('A', 'D'), ('B', 'C'), ('B', 'D')]`
```

### Filtering Iterators
`itertools.compress(data, selectors)`
Filters based on `selectors`
```
from itertools import compress

data = ["apple", "banan", "cherry"]
selectors = [1, 0, 1]

result = list(compress(data, selectors))
print(result) # ["apple", "cherry"] filters out banana
```

`itertools.dropwhile(predicate, iterable)`
Drops values until the predicate is `False`, then yields the rest
```
nums = [1, 2, 3, 4, 5, 6]
result = lsit(dropwhile(lambda x : x < 4, nums)) #drop while the num < 4
print(result) # [4, 5, 6], drops the 1, 2, 3
```

then the opposite of that is `takewhile`. 

`itertools.filterfalse(predicate, iterable)`
```
nums = [0, 1, 2, 3, 4, 5]
filtered = list(filterfalse(lambda x : x % 2, nums)) # keep evens 
# get rid of false
```
#### Grouping and Chaining
`itertools.groupby(iterable, key=none)`
groups consecutive elements that share the same key
```
data = "aaabbcddd"
grouped = [(k, list(g)) for k, g in groupby(data)]
print(grouped)
```
`[('a', ['a', 'a', 'a']), ('b', ['b', 'b']), ('c', ['c']), ('d', ['d', 'd', 'd'])]`

`itertools.chain(*iterables)`
```
result = list(chain([1, 2], ["a", "b"]))
[1, 2, 'a', 'b']
```


#### Accumulating and Counting

`itertools.accumulate(iterable, func=operator.add)`
```
nums = [1, 2, 3, 4]
result = list(accumulate(nums))
[1, 3, 6, 10]
```

`itertools.pairwise(iterable)`
```
nums = [ 1, 2, 3, 4 ]
print(list(pairwise(sum)))
#[(1, 2), (2, 3), (3, 4)]
```

|**Category**|**Function**|
|---|---|
|**Infinite**|`count()`, `cycle()`, `repeat()`|
|**Combinatorics**|`permutations()`, `combinations()`, `product()`|
|**Filtering**|`compress()`, `dropwhile()`, `takewhile()`, `filterfalse()`|
|**Grouping**|`groupby()`, `chain()`, `zip_longest()`|
|**Counting**|`accumulate()`, `pairwise()`|


There is a lot more to `itertools` that is a break down. 


#### Difference Between Iterable and Iterator
`Iterables` need `__iter__()` that returns an `iterator` - `iterables` can be iterated over, but does not do the iterating. 
`Iterator` - does the actual iterating, has the `__next__()` method