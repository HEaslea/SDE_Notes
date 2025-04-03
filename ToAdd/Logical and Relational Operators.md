Logical operators take any type that can be converted to bool. 
Relational take arithmetic or pointer type. 

Operands to these operators are rvalues and the result is an rvalue too. 

![[Pasted image 20240217145837.png]]
Remember with OR, that the right hand side will only be evaluated if the left hand side is false. And the RHS with AND will only be evaluated if the left hand side is true as well. 

`index != s.size() && !issapce(s[index])`
check that index has not reached the end of its associated string, AND if the indexed element in the string is not a space. 

If we make something a reference, then we refrain from making a copy, just refer straight to it. 

The NOT operator, imagine if `vec` is a vector of `ints`, we might use the logical NOT operator to see whether `vec` has elements by negative the value returned by empty: 
```
print first ele in vec if there is one
if(!vec.empty()) 
	cout << vec[0]
```
Simple.

The Relational Operators: 
There is a surprise when chaining these together. 
`if (i < j < k)`
This will only evaluate if k is greater than 1. As the condition groups i and j to the left and the first < operator. Therefor the k is compared to the true or false result of the first comparison, if we wanted to do this the proper way, then we would be looking at this: 
```
if (i < j && j < k)
```
As this requires the k > i by definition. 
For the first one, j could be < than i and the if would still turn true, as k > 0 too, meaning that as long as k > 1, then this would always return true. 

Testing the truth of an object or a pointer, then we use it as the condition: 
`if (val) {};` True if val is any nonzero object.
`if (!val) {}` True if val is 0. 

However if we were to say something like this `if (val == true)` this will only evaluate if val == 1. If val is not a bool, then true is converted tot he type of val before the == operator can be applied to it. 
Stay away from using the bool literals when comparing, they tend not to work too well. 