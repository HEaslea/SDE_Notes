****
The General Rule: 
**Everything in python is passed by object reference** - but it behaves like pass-by-value of the reference. 
**** 

So what does this actually mean. 

When we pass to a function, you're passing a reference to the object. 
Whether it behaves/feels like this depends on: 
1. Whether the object is mutable or immutable
2. Whether the function mutates the object or rebinds it. 

##### Immutable Types
`int, float, str, tuple`
Can't mutate them, so changes inside the function don't affect the original. 

```
def f(x): 
	x = 10 # rebindingi - original stays unchanged
	
a = 5
f(5)
print(a) # 5
```

Even if there is no rebind, remember that Python will create a new object when we try to modify an immutable type, as that is the only option: 
```
def f(x): 
	x = x + 1

a = 5
f(a)
print(a) # Still 5
```
There is no manual rebind here, as you can't avoid it with immutable types. 


##### Mutable Types 
`list, dict, set, custom objects`
They can be changed, therefore, any changes in the function will affect the original, therefore we would have to rebind in order for that not to happen. 

```
def g(lst): 
	lst.append(59) # mutates the list

my_list = [1, 2]
g(my_list)
print(my_list) # [1, 2, 99] changes have retained from function
```


