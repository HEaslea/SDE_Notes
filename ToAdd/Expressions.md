*unary* and *binary* operators. 
Unary : `&` and`*`. `*` is a dereference. 
Binary : `==` and `*` will act on two operands. 

Function calls can handle an unlimited number of operands. 

`*` are used in both, unary and binary, very context dependent. 

**Precedence** 
**Associativity**
**Order of Evaluation** of the operands. 
The result of: 5 + 10 * 20 /2; really depends on the order. 

Binary operators usually expect the same type. 
They can be used on different types, however, they have to be converted to a common type. 

We can convert an integer to a floating-point, and vice versa, yet we cannot convert a pointer type to a floating-point obviously.

What might be strange is that bool, char, short etc. are all generally promoted to a larger integral type, typically int. 

The language defines what the operators mean when applied to build-in and compound types. 
we can also define what most operators mean when applied to class types. 
The IO library << and >> operators and the operators we used with strings, and vectors and iterators are all overloaded operators. 

## Lvalues and Rvalues

Every expression is either an `rvalue` or an `lvalue`, names that are inherited from C, with a simple mnemonic purpose. 
Harder to distinguish in C++. 
Overall, when we use an object as an `rvalue`, we use the object's value (its content). When we use an object as an `lvalue`, we use the object's identity (it's location in memory). 

Operators will all differ as to whether they want an `lvalue` or an `rvalue`. 
We can use an l when we need an r, but not an r when we need an l. 
Think of it as left is more important than right. 
When we use an l in place of an r, the object's contents (its value) are used. 
We have already used several operations that use the `lvalue`. 

![[Pasted image 20240216220435.png]]When we use `decltype` to an expression, we yield a reference type. 
if the value category of expression is lvalue, then decltype yields &T;

Imagine this, if p is an int*, then `decltype(*p)` is an `int&`. 
However if we have `decltype(&p)` is an `int**`, pointer to a pointer to type int. The idea is sort of like saying that `&p` is a "pointer" (loosely), then we would get a pointer to a  pointer if that makes sense. 
However, if we have `decltype(pointer)`, then that would return a pointer. 

`typdef int *ip;` ip is a pointer to an int. 
```
typedef int *ip;

int i = 0; 
ip f = &i; 
cout << *f;
```

### Precedence and Associativity
An expression with two or more operators is a **compound expression**. 
Grouping occurs, associativity and precedence dictate how this is done. 

Overriding rules with parentheses. Subexpressions from an expression, the value will change. 

Ie. addition is lower precedence than multiplication and division. 

- Because of precedence, the expression is `3+4*5` is 23, not 35. 
Seems really obvious. 

An expression like this is: 6 + 3 * 4 / 2 + 2
Results to 14 in C++, as it is equivalent to `(( 6 + (( 3 * 4 ) / 2 )) + 2`
We can override the normal grouping with parentheses. Treat the parentheses as a unit, and then using grouping as per usual: 

![[Pasted image 20240217123548.png]]

```
int ia [] = {0,2,4,6,8}; 
int last = *(ia + 4); // initializes last to 8, the value of ia[4]
last = *ia + 4; // last = 4, equivalent to [0] + 4
```




![[Pasted image 20240217124015.png]]
Just keep reading and looking at this. 

![[Pasted image 20240217124712.png]]

And this lol, re read through the array chapter if you think you don't really understand it, little bit tricky to completely understand first time through. 

Associativity most matters for input and output expressions. 
IO operators are left associative. 
`cin >> vi >> v2;` read into v1, then v2. 

## Order of Evaluation
`itn i = f1() * f2();`
We know that f1 and f2 must be called before the multiplication can be done. There is no way of knowing whether f1 will be called before f2 or not. 

```
int i = 0; 
cout << i << " " << ++i << endl;
```
This is strange as it outputs 1 1: not 0 1. 
This is undefined behavior. 
The general idea is that we cannot guarantee the order of operations in this line. 
However, there are four operators that do guarantee the order in which operands are evaluated. 
&& will evaluate the left hand operand is evaluated first. The right hand operator is evaluated  only if the left hand is True.

The logical or (`||`), the conditional (`?:`), and the comma operator. 

### Order, Precedence and Associativity

`f() + g() * h() + j();`
Precedence means that the results of g() and h() are multiplied first. 
Associativity guarantees that the result of f() is added to the produce of g() and h() and that the result of that addition is added to the value of j(). 

There are NO guarantees to the order in which the functions are called. 
However, the order of function call is irrelevant. 

When in doubt, parenthesize to force the grouping that the logic of your program requires. 

If you change the value of an operand, don't use it elsewhere in the same expression, hoping that it will work the same way on every machine. 

### Arithmetic Operators
eg. Multiplication over addition, and operators of higher power group more "tightly" than lower precedence operations. They are all left associative (meaning that operations are grouped from the left). 

![[Pasted image 20240217144343.png]]

Applied to any arithmetic type, or a type that can be converted to an arithmetic type. Operands might be converted to a common type as part of evaluating these operators. 

Unary plus and minus applied to pointers. These operators are essentially functions that will return a value. 

Here is a cool idea of type conversion reaching a strange value: 
```
int i = 1024; 
int k = -i; is -1024 as expected
bool b = true; 
bool b2 = -b;
cout << b2 << endl;
```
The result of this is that b2 is 1, the reason being is that bool is promoted to int, true = 1, therefore the - makes it -1, then it will be converted back to a bool, meaning that it goes back to 1. You could think about it as, the thing exists, therefore it's true. 
Think about the conversion here. 

Think about overflow as well. 

Remember with modulus: m%(-n) = m%n and (-m)%n is equal to -(m%n). 


