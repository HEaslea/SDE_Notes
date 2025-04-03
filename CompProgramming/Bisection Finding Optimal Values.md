Bisection - is just binary search for the optimal value: 

```
def valid(x): 
    return x > 100

print("Start")
start = -1

step = 200

while step > 0: 
    print(f"{start} step: {step}")
    if not valid(start + step): 
        start += step
    else:
        step //= 2

print(start + 1)

print("End")
```

This means that when we are above 100, we return true, meaning that we have to half the step that we are using by 2. 
If we are below, meaning we return `False` then we have to step forward by whatever the step amount is. 

```
-1 step: 200
-1 step: 100
99 step: 100
99 step: 50 
99 step: 25 
99 step: 12 
99 step: 6  
99 step: 3  
99 step: 1  
100 step: 1
```

This it the output for this, as we go over and we are valid, so half, then again, then we get to 99, then plus 100, so half, then 50, then half etc. etc. until we get to 1, then we have 100. 


