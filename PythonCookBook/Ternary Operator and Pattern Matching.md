### Conditional Expressions
As in ternary operators are implemented using conditional expressions: 
`value_if_true if condition else value_if_false`
Like a small `if : else` statement: 

```
order_total = 247

if order_total > 100: 
	discount = 25
else: 
	discount = 0
print(order_total, discount)
```

Just to: 
```
discount = 25 if order_total > 100 else 0
```
Isn't that just great english pretending to be python, fantastic so far. 

We will be spending more time reading than writing, so making sure that this is all legible, is the main thing, python naturally does this. 

In C++, tbh, it's even more concise: 
`int discount = (order_total > 100)? 25 : 0;`
or `std::string result = (x > 0)? "Positive" : "Negative"`
We can take this further with: 
`std::string result = (x > 0)? "Positive" : (x < 0) ? "Negative" : "Zero";`

## Pattern Matching
##### Basically C++ Switch
In C++, only works with integral types (`int, char, enum`)
```
int x = 2; 
switch(x) 
{ 
	case 1 : std::cout << "One"; break;
	case 2 : std::cout << "Two"; break;
	default: std::cout << "Other";
} 
```

However in python, it will match pretty much anything that we have: `int, strings, lists, tuples, objects etc. `
```
x = 2
match x: 
	case 1: print("One")
	case 2: print("Two")
	case _: print("Other") # default case
```

More: 
```
day_number = 4
match day_number: 
	case 1 | 2 | 3 | 4 | 5: 
		print("Weekday")
	case 6: 
		print("Saturday")
	case 7: 
		print("Sunday")
	case _: 
		print(f"{day_number} is not a valid day number")
```
The key to remember here is that **there is no fall-through** like in C++. 
Therefore there is no need to `break;`. 

