`cond ? expr1 : expr2`

If cond is true, then we expr1 is evaluated, if not than expr2 is evaluated. 

`string finalgrade = (grade < 60)? "fail" : "pass"`
Guaranteed that only one of the expr are evaluated. 

### Nesting Conditional Operators
```
finalgrade = (grade > 90)? "high pass" : (grade < 60)? "fail" : "pass";
```
You can see how this is nested, in that if the first conditional is false, then we move to asking if the grade is < 60. 
This will quickly become unreadable, do not really nest more than 2 or 3 in a row. 

Conditional Operators in an Output Expression: 
```
cout << ((grade < 60)? "fail" : "pass"); // print pass or fail
cout << (grade < 60)? "fail" : "pass"; // prints 1 or 0
cout << grade<60? "fail" : "pass"; // error 
```
In the second expression, the answer to grade < 60 is used, so either 1 or 0 is printed. 
As << has a higher precedence than the conditional operator, in the last one, the cout will print out grade first, then it will be compared to 60, `cout < 60 etc.` which will result in an error.
