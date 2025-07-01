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

