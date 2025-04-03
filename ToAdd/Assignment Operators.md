
The left hand operator must be a modifiable lvalue: 
```
int i = 0, j = 0, k = 0; // initializations, not assignments
const int ci = i;  // init, not assignment again
```

These below are all illegal: 
```
1024 = k; literals are rvalues
i + j = k; arithmetic expressions are rvalues
ci = k; ci is a const (nonmodifiable) lvalue
```

The right hand operand in assignments, is converted to the left. This is pretty important when you think about how values will change if you have difference in types. 

Assignment is Right Associative
```
int ival, jval; 
ival = jval = 0; 
```
0 is assigned to jval, and jval is assigned to ival.
Of course there must be the same typing through the whole assignment. 

```
int ival, *pval; 
ival = pval = 0; cannot assign the value of a pointer to an int
strint s1, s2; 
s1 = s2 = "Ok"; fine as literal "OK" converted to string
```


Assignment has low precedence

```
int i = get_value(); 
while (i != 42){ 
	do something
	i = get_value();
}
```
Although this works, it's better to write: 
```
int i; 
while((i = getvalue()) != 42){ 
	do something 
}
```
Can you see how this makes it more obvious what is going. 
Without the parentheses, i would be the result of the true or false test. 
`i = getvalue() != 42` Remember that we start from the right, therefore does 42 not equal our getvalue(), then i would be assigned to that, not really a condition either. 

```
int sum = 0; 
forF(int val =1; val <= 10; ++val){ 
	sum += val;
}
```
```
+= -= *= /= %= // arithmetic operators
<<= >>= &= ^= |= // bitwise operators
```

### Increment and Decrement
There is postfix and prefix. The prefix will inc or dec and yield the changed object as its result.  
The postfix will yield the original.

![[Pasted image 20240217160209.png]]
This will out 0, then 2. 

They require lvalue operands. 

```
auto pbeg = v.begin(); 
// print first elements up til a negative value
while(pbeg != v.end() && *beg >= 0){ 
	cout << *pbeg++ << endl; // print the current value and advance pbeg
}
// remember that *pbeg++ is the same as *(pbeg++), the increment will yield the original value. 
```
It is worth noting that this can be more readable from a glance to people: 
```
cout << *iter << endl;
++iter;

// over

cout << *iter++  << endl; 
```

Remember that operators give no guarantee to which order they are evaluated. 

This usually doesn't matter. 
The issue is when we change the value in one subexpression and then that value is used in another subexpression. 
eg. 
```
for (auto i = s.begin(); it != s.end() && !issapce(*it); ++it){
	*it = toupper(*it); 
} // this is fine, if we were to write it like this

while (beg != s.end() && !issapce(*beg)){ 
	*beg = toupper(*beg++); //  error! this assignment is undefined}
```
This is because the increment will change the value of beg, and the assignment is undefined. 

