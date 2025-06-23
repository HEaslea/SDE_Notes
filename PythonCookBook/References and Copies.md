A great way of showing the difference is with a list and how we can copy, or just reference the same list. 

There are important differences between Python and C++ in this case.
#### References
```
a = [1, 2]
b = a # b is a referenc to a
# any changes to b will also change a, this isn't obvious because in C++ this is not the case at all, and the copy constructor would be used in thsi case

b.append(3)

print(a) # [1, 2, 3]
```

#### Copying
With lists, the easiest way to copy is to do `[:]`. 

```
a = [1, 2]
b = a[:] # b is a copy (shallow)
b.append(3)
print(a) # [1, 2] - unaffected
```

### Deep and Shallow Copy
**Shallow Copy**: - Copies the top level object, **shares reference to nested objects inside**. 

**Deep Copy**: - Copies **everything recursively**, including all nested objects. 

Consider: 
```
import copy # allowing us to use .copy() and .deepcopy()

original = [[1, 2], [3, 4]]

shallow = copy.copy(original)
deep = copy.deepcopy(original)

original[0][0] = 999

print(shallow) # [[999, 2], [3, 4]] <-- affected! 
print(deep) # [[1, 2], [3, 4]] <-- affected! 
```
It's clear that the lists inside. 

### Interning
Memory optimization technique where small or frequently used immutable Objects are reused instead of creating new ones each time. 
Applying to small integers, some strings. 
```
a = 100
b = 100
print(a is b) # True - both point to the same object
```

```
x = 1000
y = 1000
print(x is y) # Fase - different objects (outside interned range)
```

An important distinction: 
```
s1 = "hello"
s2 = "hello"
print(s1 is s2) # True - interned string literals
```

The distinction: 
```
s3 = "".join(["he", "llo"])
print(s3 == s1) # This is True, as they have the same contents
print(s3 is s1) # False, different objects (not automatically interned)
```

`is` is faster than `==`. 
##### Manually Interning
```
import sys
sys.intern("some_string")
```

### Why Intern? 
Only in specific cases: 
Python will only do this when the objects are immutable, and only under certain conditions. 
Optimization mainly. 

[[Immutable and Mutable Objects]]
