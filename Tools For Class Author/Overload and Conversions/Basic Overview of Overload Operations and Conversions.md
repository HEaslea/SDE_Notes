Defining the meaning of operators when applied to operand(s) of a class type. 

Functions with special names: 
	`operator` followed by the symbol for the operator that we want to be more defined. 

Return type, parameter list and body. 

An overloaded operator function has the same number of parameters as the operator has operands. 
Unary, taking in one parameter. 
Binary taking in two duh. 

Except for `operator()` we do not have default arguments. 

The left hand is bound to the implicit `this` pointer. 
The first operand is implicitly bound to `this`, a member operator function has one less (explicit) parameter than the operator has operands. 

So just remember that `this` is bound to the left hand operand. 

An operator function therefore,  must either be a member of a class, or have at least one parameter of class type (therefore we can overload).

```
// error cannot redefine the built-in operator for ints
int operator+(int, int);
```
This does mean that the built-in types cannot be overloaded or overridden. 

You can overload so many operators, but not a couple. 
![[Pasted image 20240424155235.png]]You can even overload `new` and `delete`. 

You can't just make up new operators either, the idea of `operator**` will not work. 

Some symbols, serve as both unary and binary operators. 
They can both be overloaded, either or. Here the number of parameters here will determine which one we are overloading. 

An overloaded operator has the same precedence and associativity as the corresponding built in operator (sort of life the operator has a built-in of its own). 
Regardless of operand types: 
`x == y + z`;
Is always going to be equivalent to `x == (y + z)`
You will not be able to change precedence and associativity, they are just the way that the operators are used (universally as well). 

#### Calling an Overloaded Operator Directly
Normally, we do this indirectly, using the operator on the arguments of the appropriate type. 

We just name the function and pass an appropriate number of args over to it: 
```
// these are equiv calls
data1 + data2; // normal expression
operator+(data1, data2);
```

They both do the same thing here: 
Binding this to the address of `data1` and passing `data2` as the argument. 


#### Some Shouldn't Be Overloaded
Don't overload operators that do not preserve order of evaluation when they are overloaded. 
Users are going to be weirded out when the usual guarantees they are accustomed to are not honoured for code that happens to use an overload version of one of these operators. 

Obviously don't overload shit like `&`, it will just throw people off completely. 

> So normally , the comma, address-of, logical `AND`, logical `OR`, should not be overloaded. 

#### Use Definitions that are Consistent with the Built-In Meaning

When designing a class, think first about what operations the class will provide. 
Do we want to overload an operator (really?) we could just make it a function. 

- The class does IO, define the shift operators to be consistent with how IO is done for the built-in types. 
- If the class has an operation to test for equality, define `operator==`. Therefore, we should usually have a `opreator!=` as well. 
- If the class has a single, natural ordering operator, define `operator<`. If the class has the `operator<`, we should probably have all of the relational operators.
- Return type of overloaded operator usually should be compatible wit he return type from the built-in version of the operator. The return type of relational operators should return `bool`, arithmetic ops should return a value of the class type (usually), and assignment and compound assignment should return a reference to the left hand operand. 

#### Assignment and Compound Assignment Operators
After the assignment, the values in the left hand and right hand operands should have the same value, and the operator should return a reference to its left hand operand. 

Just follow somewhat to what each operation means, like `+` obviously means addition, so don't do some weird shit where `+` adds the object to a container, that could just be in some function. 

Abusing operators in this way, no one would use `operator+` in order to subtract, that would be insane. 
Just don't make them ambiguous. 

So if we have addition, usually a good idea to keep the operator `+` in line wit addition. 

#### Make it a Member or Non-Member? 
Do we make the overloaded operator a class member or an ordinary nonmember function. 

Sometimes there is no choice, some operators are required to be members; in other cases, we may not be able to define the operator appropriately if it is a member. 

The following guidelines can be helpful. 

- The assignment (=), subscript([]), call(()), and member access arrow (->) operators **must be defined as members**. 
- The compound-assignment operators ordinarily **ought** to be members. However, unlike assignment, they are not required to be members. 
- Operators that change the state of their object or that are closely tied to their given type - such as increment, decrement, and dereference - usually should be members. 
- Symmetric operators - those that might convert either operand, such as the arithmetic, equality, relational, and bitwise operators - usually should be defined as ordinary nonmember functions. 

Symmetric operators are those that the order of the operands does not affect the result. eg. commutativity of equations
`b * a = a * b`, `a + b = b + a`
However, obviously `b - a != a - b` unless `b = a`. 

We expect to be able to use symmetric operators in expressions with mixed types: 
eg. `int` and `double`. 

If we want to be able to provide similar mixed-type expressions involving class objects, then the operator must be defined as a nonmember function, remember that class member functions will utilise the LHO (left hand operands) and bind it to `this`. 

When we define an operator as a member function, the LHO must be an object of the class of which that operator is a member: therefore symmetry there is a little tricky. 

```
string s = "world";
string t = s + "!"; // ok we can add const char* to string 
string u = "Hi" + s; // would be an error if + were a member of string
```

If the `operator+` were a member of the `string` class, the first addition would be equiv to `s.operator+("!");`. 

`"hi" + s` would be equiv to `"hi".operator+(s)`, but here "hi" is a `const char*`, which doesn't even have member functions. 
So when we have the overload operator in the class, and we call it implicitly by this idea `type.operator+(RHO)`. That can cause issues as we see above.

`string` defines + as an ordinary non member function, `"hi" + s` is equiv to `operator+("Hi", s)`. 

If we have that `operator` overload, whatever it is, we can just overload it, and then argument matching, rather than using ADL to go into the classes and find the right one there. 

