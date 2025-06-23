#### Immutable Objects
- `int`
- `float`
- `bool`
- `str`
- `tuple`
- `frozenste`
- `bytes`

```
x = "hello"
print(id(x)) # eg. 14234534636
x += "world"
print(id(x)) # will be a different ID - new string created
```

Even though it seems that `x` has just been updated, that is not the case. 

### Mutable Objects
- `list`
- `dict`
- `set`
- `bytearray`

```
x = [1, 2, 3]
print(id(x)) 
x.append(4) 
print(id(x)) # will be the same id, modified in place
```

##### Common Pitfalls
1. Shared References with mutable types
```
a = [1, 2]
b = a
b.append(3)
print(a) # [1, 2, 3] - becauase 'b' and 'a' point to the same list
```

2. Tuples with mutable elements
`tuples` are immutable, however, they can contain mutable objects
```
a = ([1, 2], 3)
a[0].append(99)
print(a) # ([1, 2, 99], 3)
```


You can just use `id()` to check. 

Remember that **everything in Python is a reference**by default - but with behaviour that depends on mutability. 

The main thing to remember and think about is that `=`, in Python, **never creates a copy**. 

```
b = [1, 2, 3]
a = b # a and b point to the same list as we have before
```

Therefore a copy is only created, when we explicitly ask for it. 

```
b = a[:] # shallow
b = list(a) # shallow
b = a.copy() # shallow
import copy
b = copy.copy(a) # shallow 
b = copy.deepcopy(a) # shallow 
```

##### Immutable Types
Even for immutable types, `=` will still be a reference. 
This will not really matter and you won't fall into pitholes, as you can't change these types anyways, anytime you do, it creates a new object. 

**New Objects on Modification**: for immutable types
Even though it feels like we are modifying the variable, just like it is in C++, in Python, we are actually: 
> **Creating new Objects in memory and rebinding the variable to point to it**

```
x = "hello"
print(id(x)) 

x += " world" # createas a new string object
print(id(x))
```

**Garbage Collecting**: 
When we make an object, we are marking something in memory that python has to keep track of. 
Therefore, when we modify that, we are making a new object. 
The memory of that first object, is useless now. 
Therefore we need garbage collecting in order to get rid of it, without doing it explicitly ourselves. 

```
x = "hello"
x = "goodbye" # "hello" has no references now
# therefore
#GC will eventually clean it up
```

CPython, the standard Python implementation. 
- Referencing counting clears memory immediately in most cases (therefore no waiting). 
- A cycle detector handles more complex cases (self-referencing objects). 
