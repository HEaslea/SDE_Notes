## Positional Parameters 
Most common, arguments provided in the correct order: 
```
def greet(name, age): 
	print(f"Hello, {name}! You are {age} years old")
	
greet("Alice", 25)
greet(25, "Alice") # works but isn't right
```

## Default Parameters
```
def greet(name="Guest"):
	print(f"Hello {name}!")

greet()
greet("Bob")
```

## Keyword Arguments
```
def describe_pet(animal, name): 
	print...

describe_pet(animal="dog", name="Buddy")
describe_pet(name="Buddy", animal="dog") # the order doesn't matter
```

## Variable Positional Arguments (`*args`)
```
def add_numers(*args): # will pack into a tuple
	print(args) # tuple of values
	return sum(args)
print(add_numbers(1, 2, 3)) # 6
print(add_numbers(4, 5)) # 9
```

## Variable Keyword Arguments (`**kwargs`)
Allows passing a variable number of keyword arguments as a dictionary
```
def print_info(**kwargs):  # will pack into a dictionary
	print(kwargs)

print_info(name="Alice", age=30, city="London")
# will come in as a dictionary 
```

## Position-Only Parameters (`/`)
Parameters before `/` must be provided positionally. 
```
def greet(name, /, age): 
	print(f"{name} is {age} years old.")

greet("Alice", 25) # this is fine
greet(name="Alice", age=25) # Error name must be positional
```

## Keyword-Only Parameters (`*`)
```
def greet(*, name, age): 
	print(...)
greet(name="Alice", age=25) # good and fine
greet("Alice", 25) # Error, must use keywords
```


##### Remember 
`*` Anything **AFTER** must be keyword. 
`/` Anything **BEFORE** must be keyword. 

Anything **BETWEEN** them can be either, as in below, `pos_or_kw`. 
### Combining Them All
```
def complex_function(pos1, pos2, /, pos_or_kew, *, kw_only1, kw_only2="default"): 
	...

complex_function(1, 2, 3, kw_ony1="Required") # ok
complex_function(1, 2, pos_or_kw=3, kw_only1="required") # ok aswell. 
```

```
def test_func(**dict_k): 
	for key, value in dict_k.items(): 
		print(f"{key} ++ {value}")
		
test_func(i=10, j=20) # This works fine

dicta = {
	"i":10, 
	"j":20
}
test_func(dicta) # This WON'T WORK, can't pack an already packed dict
test_func(**dicta) # unpacking to repack would work
# the second form of this seems like an edge case, however, the idea stands
# you will have to punpack a dictionary in order to 
```

Which is equivalent to: 
```
template <typename T, typename ... Args> 
void test_func(T first, Args ... args)
{ 
	std::cout << first << " +++ ";"
	if constexpr(sizeof...(args) > 0)
	{ 
		test_func(args...); // Recursive unpacking
	}
}

// or
template <typename ... Args>  // packing
void test_func(Args ... args) // packing
{ 
	((std::cout << args << " +++ "), ...); // Fold Expression for this (with comma operator included)
}
// this second one is a little nicer


// more
template<typename ... Args> 
void test_func(Args ... args)
{ 
	// fold expression with structured bindings
	((std::cout << args.first << " +++ " << args.second << "\n"), ...);
}
// then 
test_func(std::pair{"i", 10}, std::pair{"j", 20});
```


## Modifiable Defaults
Modifiable Defaults changes will persist between function calls, this is because **modifiable defaults are evaluated once at function definition, not at each call** : 
```
def add_to_list(item, my_list=[]): # mutable default argument
	my_list.append(item)
	print(my_list)

add_to_list(1)
add_to_list(2)
add_to_list(3) # will be [1, 2, 3]
```
This is just because that default is only evaluated once during the definition of the function. 
To get around this, then use `my_list=None`
Then define `my_list` in the function. 

Super useful when using Memoization!!!!
The defaults will be retained even if we use another list. 
```
def default_test(a, my_list=[]):
    my_list.append(a)
    print(my_list)

default_test(1)
default_test(2)

default_test(6, [9, 8, 7])

default_test(3)
```
