The absence of a value or a null value. 
As in no value `nothing`. Not 0, nothing, no-thing. 

#### Usage in Assignments and Functions
```
def example(value=None): 
	if value is None: 
		value = "Default"
	print(value)
```

If a function doesn't explicitly return a value, it returns `None` by default: 
```
def no_return(): 
	pass
result = no_return()
print(result) # Prints None
```

#### Conditional Checks
`if None: ` always `False`

Also: 
```
my_var = None
if my_var is None: 
	print("It is None!")
```

#### None in Collections
```
my_list = [1, 2, None, 4]
my_dict = {'key1' : None}
my_set = {None, 1, 2}
```

#### None and `NoneType`
`None` is the sole instance of the `NoneType` class. 
`None` cannot be created or instantiated manually. 
`None` $\therefore$ is very very special. 

#### Common Scenarios
When a function doesn't return a value: it will return `None` (so it does, but `None` is no value, so it doesn't). 

Can be to indicate the end of a list or for an empty placeholder: 

Setting default parameters: "no value" - meaning that we get a new one every time, and not just one for the first time that the function is evaluated. 
```
def get_user_input(prompt, default=None): 
	user_input = input(prompt)
	if user_input == "": 
		return default
	return user_input

```