The `.` will access an object of class type; arrows is defined so that `ptr->mem` is a synonym for `(*ptr).mem`. 

```
string s1 = "a string", *p = &s1; 
auto n = s1.size(); 
n = (*p).size(); // runs on the object to which p points
n = p->size(); // equiv to the line above
```
DEREFERENCE HAS A LOWER PRECEDENCE THAN DOT, that's why we have the () around `*p`. 

`*p.size()` is erroneous, as p does not have a member `size()`. 

The arrow therefore does require an arrow operand and yields an lvalue. The dot operator, yields an lvalue if the object from which the member is fetched is an lvalue; otherwise the result is an rvalue. 

