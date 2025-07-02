### List Comprehension
`[expression for item in iterable if condition]`

`[x for x in range(10)]` - will go from `0 - 10`

`[x**2 for x in range(5)]` - `[0, 1, 4, 9, 16]`

`[x for x in range(10) if x % 2 == 0]` - `[0, 2, 4, 6, 8]`


###### Double Comprehension
```
matrix = [[1, 2, 3], [4, 5, 6]]
flattened = [num for row in matrix for num in row] # confusing at first
#[1, 2, 3, 4, 5, 6]
```
The best way to think about these is to expand them in your head. 
The first bit is the outside bit.

Also,  `[<what we are taking in the list> for something in something for <thing> in thing]`

**THE FIRST BIT WILL BE NESTED INSIDE**
```
for row in matrix: 
	for num in row: 
# split them up, num -> for row in matrix : for num in row
```

I would also think about this a little as well: 
```
f = [n for x in range(5) for n in range(5)] # here we are not using x
# think about this as  : for x in range(5): for n in range(5): append(n)
[0, 1, 2, 3, 4, 0, 1, 2, 3, 4, ...] # we are clearly following n's movement

n[x for x in range(5) for n in range(5)] # here we are not using n
# think about this as : for x in range(5) f: for n in range(5) : append(x)
[0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 3, 3, 3, ... ]
```

Read it how it is written in that the first part of the `for` is the outer part, then the next part is the inner part and you can take either of two, or both of the expressions. 

Then we can combine them: 
```
test = []

for x in range(1, 5): 
	for n in range(5): 
		test.append( x * n )
```
Is the same as: 
`f = [x * n for x in range(1,5) for n in range(5)]`
`[0, 1, 2, 3, 4, 0, 2, 4, 6, 8, 0, 3, 6, 9, ...]` 
The multiples of 1 and greater, times by 5. 

##### Conditionals
`f = [n for n in range(10) if n% 2 == 0]`
`[0, 2, 4, 6, 8]`
(not the best way of doing this). 

### Set Comprehension
```
unique_lengths = {len(word) for word in ["hello", "world", "hi", "python"]}
# {2, 5, 6} remember no duplications in a set.
```

This works the exact same way. 

`f = {x for x in range(5) for n in range(5)}`
`# {0, 1, 2, 3, 4}` no duplicates. 

### Dictionary Comprehension

```
squares = {x : x**2 for x in range(5)}
# {0 : 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

Then: 
```
even_squares = {x : x**2 for x in range(10) if x % 2 == 0}
# {0: 0, 2: 4, 4: 16, 6: 36, 8: 64}
```


### List in List in List ... Comprehension
```
# Transposing A Matrix
matrix = [[1, 2], [3, 4], [5, 6]]
transposed = [[row[i] for row in matrix] for i in range(2)]
#[[1, 3, 5], [2, 4, 6]]
```

### Small Challenge
![[Pasted image 20250423184422.png]]


Think about what we need here: 
We need the `ord(x) for x in word`  and that's `word for word in words`
And only if `word.islower()` and `word.isalpha()`
Therefore
```
asciiDict = {
	word : [ord(c) for c in word]
	for word in words
	if word.islower() and word.isalpha()
}
```

Another one is : given a list of sentences, build a dict that maps each lowercase vowel to a list of counts, where each count corresponds to how many times that vowel appears in each sentence. 

```
sentences = [ 
	"Hello there!", 
	"Python is fun.", 
	"A quick brown fox jumps over the lazy dog.", 
	"AEIOU are vowels.", 
	"Sometimes Y..."
]
```
```
{
    'a': [0, 0, 1, 1, 0],
    'e': [3, 0, 3, 3, 0],
    'i': [0, 1, 1, 1, 0],
    'o': [1, 1, 4, 2, 0],
    'u': [0, 1, 2, 1, 0]
}
```

You would otherwise use `collections.Counter()` for this, however, as list comprehension. 

So we have a `Dict`
```
tDict = 
{ 
	vowel : [s.lower().count(v) for s in sentences]
	for v in vowels
}
```

### Nested Dict Comprehension
This is blowing my mind a little bit: 

We might have this idea : which is evident in the book as well. 

```
students = ['Alice', 'Bob', 'Charlie']
subjects = ['Math', 'English', 'History']

grades = {student: {subject: None for subject in subjects} for student in students}

# so a nested dict that looks something like this
```
```
{
  'Alice':   {'Math': None, 'English': None, 'History': None},
  'Bob':     {'Math': None, 'English': None, 'History': None},
  'Charlie': {'Math': None, 'English': None, 'History': None}
}
```

We can see the `None` in there. 
Then this is very easy to update: 
`grades['Alice']['Math'] = 95`
So `<name of outer dict>[<key of outer dict>][<key of inner dict>]`

The expanded version: 
```
grades = {}

for name in students: 
	grades[name] = {}
	for subject in subjects: 
		grades[name][subject] = None
```

### Filtering a Comprehension
First, let's look at using `filter()`. 
Remember this is what a filter looks like, what it returns and how to use it in a simple manner: 
```
a = filter(lambda x : x % 2, [x for x in range(50)]) # get only odds here

print(type(a))
print(a)
print(list(a))
```

```
<class 'filter'>
<filter object at 0x0000029195e55ab0>
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23, 25, 27, 29, 31, 33, 35, 37, 39, 41, 43, 45, 47, 49]
```

```
form math import sqrt
mx = 10

triples = [ # creating all the triples, `c` is likely a real
	(a, b, sqrt(a**2 + b**2)) # making a triplet
	for a in range(1, mx) # this is the outer loop
	for b in range(a, mx) # this is the inner loop
]

# this will filter out all the non-pythag triples
triples = list( 
	filter(lambda triple: triple[2].is_integer(), triples)
)

print(triples) # prints: [(3, 4, 5.0), (6, 8, 10.0)]
```

Triples aka three-tuples. 

The problem here is that we are creating a list with some useless values in, then filtering out. 

We can use `map()` in an alternative form here. 

```
from math import sqrt

mx = 10

triples = [
	(a, b, sqrt(a**2 + b**2))
	for a in range(1, mx)
	for b in range(a, mx)
] # here we have reals in the triples

# let's make them integers
triples = filter(lambda triple : triple[2].is_integer(), triples) # Line X

triples = list( # remember that map gives a map object Line Y
	map(lambda triple : triple[:2] + (int(triple[2]),), triples)
	# looks like we have a rogue comma, but without it this doesn't work, see below
)
```
`Line X:` we have triples of `a, b, a**2 + b**2` : then we are filtering out, getting a `filter` item, we are only taking forward. 

`Line Y:` we take that `filter` object, and then we make a `list` of it, before we do that we `map` :  for every triple we want to actually cast `triple[2]` to an `int`. Therefore the return type needs to be `triple[:2]`  (remember that `slicing` is non-inclusive, so this will be the first 2 elements). 
We want to cast to `int` : `int(triple[2])`
The extra `()` here is to then put that into a `tuple`, that's something that is key to remember, that if we have `()`, we are "putting into a tuple, or a pair (which are essentially the same thing in python)". 
That extra comma is what separates it from being a `tuple` otherwise we just have `(int(triple[2]))` which is just an integer, with the outer parentheses being pretty useless. 
However, when we do `(int(triple[2]),)` makes it a `tuple`, this is very important indeed. 

```
lst = [x for x in range(10)]

n = map(lambda x : x**2, lst)

print(n)
print(type(n))
print(list(n))
```

```
<map object at 0x000001c6f9ee5b70>
<class 'map'>
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```


This is super complicated, however, there are much more simple practices in order to get there, that are much more pythonic: 
```
from math import sqrt

mx = 10

triples = [ 
	(a, b, sqrt(a**2 + b**2))
	for a in range(1, mx)
	for b in range(b, mx)
]

triples = [ 
	(a, b, int(c)) for a, b, c, in triples if c.is_integer()
]
```
The difference is wild. 
However, there is still room for improvement, we are still making a list and pruning, might as well prune as we are making the list. 

```
from math import sqrt

mx = 10

# combining the generating and filtering into one comprehension

triples = [ 
	(a, b, int(c))
	for a in range(1, mx)
	for b in range(a, mx)
	if(c := sqrt(a**2 + b**2)).is_integer()
]
```
here we are using the walrus operator. 
Remembering that the `:=` is walrus operator in order to assign `c` while in the comprehension. 
That part has to come in, because we have no assigned `c` yet throughout the comprehension. 

Without the walrus operator, this opens up a lot. 

```
triples = []

for a in range(1, mx):  # you can also see how the inner and outer line up vewry nicely here
	for b in range(a, mx): 
		c = sqrt(a**2 + b**2)
		if c.is_integer(): 
			triples.append((a, b, int(c))) # append asa triple here
```

You can see how this extends, lines up very nicely, and realistically, the code isn't there much longer, however, the walrus operator `:=` lets us do it inline. 

Remember that dictionaries do not allow duplicate keys. 

