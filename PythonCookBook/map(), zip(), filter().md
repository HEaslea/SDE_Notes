### `map()`#
The idea being that we apply a function to each item in the iterable and returns a `map` object (which is an iterator)
```
nums = [1, 2, 3]
squared = map(lambda x : x**2, nums)
print(list(squared)) # 1 4 9
```

### `zip()`
pairing elements from multiple iterables by **index**
```
a = [1, 2, 3]
b = ['a', 'b', 'c']
zippped = zip(a, b)
print(list(zipped)) # [(1, 'a'), ...] 
```
Only to the shortest iterable length.

There's also the idea of unzipping
```
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
a, b = zip(*pairs)
# a -> (1, 2, 3), b -> ('a', 'b', 'c')
```

### `filter()`
Keep only items that will match a condition (look at `views` in C++). 
```
nums = [1, 2, 3, 4, 5]
evens = filter(lambda x : x % 2 == 0, nums)
print(list(evens)) # [2, 4]
```

### `args` and `kwargs`
Another explanation to cement the idea in neurons. 
Given: 
```
result = map(lambda a: print(*a), zip([1, 2], ['a', 'b']))
# Output
# 1 a 
# 2 b 
```
Here we are unpacking (remember that everything after the lambda goes into the parameter list): 
IE if we have `lambda *a : a` we get `def lamdaFunction(*a): a`

So `lambda *a : a` -> looks odd but the `*a` is going into the parameter list. 
Called a variadic lamba. 
Normally a lambda will just take in for the parameters that are given : `mult = lambda a , b : a * b` meaning that we only taking in two args `mult(1, 2)` pretty obvious. 
Just the same in C++: `[](int a, int b) -> int { return a * b; };`
Then there is the variadic form: 
```
auto variadic = [](auto ... args)
{ 
	// do something here with the args parameter pack
};
```
Then just using fold expressions to unpack: 
```
auto print_all = [](auto ... args)
{ 
	(std::cout << ... << args) << "\n"; 
}; // this ; seems weird given the function-like syntax
```

```
auto forward_all = [](auto&& ... args) // universal "reference"
{ 
	some_func(std::foward<decltype(args)>(args)...); // unfold around the comma operator implied
}
```


Therefore `lambda *a : a` will return a tuple -> `def some_func(*a): a` will pack and then just return that tuple

Given this: 
```
tupler = lambda *a : a # now it's clear that this is just packing in and returning the list

print(type(tupler(1, 2, 3))) # will print 
```

Knowing all of this, given args and kwargs: 
**`args`**: packing of positional arguments into `tuple` 
`kwargs` : packing of key-word (`kw`) into a `dict`

```
def greet_all(*args): 
	for name in args: # iterate through tuple
		print(f"Hello, {name}!")
```

```
def print_info(**kwargs): 
	for key, value in kwargs.items():
		print(f"{key}: {value}")
```

Then there's the opposite where we unpack, that's just putting `*` and `**` into the args. 

```
args = (1, 2)
kwargs = {"x" : 10, "y" : 20}

def add(a, b, x=0, y=0): 
	return a + b + x + y

print(add(*args, **kwargs)) # 33
```

Reminder: 
```
std::map<int, std::string> mMap; 
// or 
std::map<int, std::string> mMap = { 
	{1, "one"}, 
	{2, "two"}, 
	{3, "three"}
};

mMap[4] = "four";

mMap.insert({5, "five"});

myMap.emplace(6, "six");
```



### More Complex Examples
```
import math

vectors = [(3, 4), (5, 12), (8, 15)]

normalized = list(map(lambda v : (v[0]/math.hypot(*v), v[1]/math.hypot(*v)), vectors)
```

```
students = ["Alice", "Bob", "Charlie"]
grades = [88, 95, 70]

paired = list(zip(students, grades)) # a list of tuples

sorted_by_grade = sorted(paired, key=lambda x: x[1], reverse=true) # highest first
```

```
lines = ["Hello", "", "  ", "World", "Python    "]

#for_each for formatting
cleaned = list(filter(lambda s : s.strip(), lines))
print(cleaned) # ["Hello", "World", "Python"]
```

