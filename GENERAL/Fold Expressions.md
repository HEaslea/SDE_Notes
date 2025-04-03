For variadic arguments: 
We can have fold expressions: 

[Fold Expression CPPRef](https://en.cppreference.com/w/cpp/language/fold)

There are four syntaxes here: 
1. `(pack op ...)` unary right fold
2. `(... op pack)` unary left fold

3. `(pack op ... op init)` binary left fold
4. `(init op ... op pack)` binary right fold

Where `pack` are operators that contain unexpanded parameter packs, and does not contain an operator with precedence lower than cast at the top level. 

Where `op` can contain any of the 32 binary operators `+ - * ->* /=` etc. etc. 

Where `init` is an expression that does not contain an unexpanded parameter pack and does not contain an operator with precedence lower than cast at the top level. 

Explanations: 

1. Unary Right Fold `(E op ...)` becomes `(E1 op(... op(En-1 op En)))`

2. Unary Left Fold `(... op E)` becomes `(((E1 op E2) op ... ) op En)`

3. Binary Right Fold `(E op ... op I)` becomes `(E1 op (... op (En-1 op (EN op I))))`

4. Binary Left Fold `(I op ... op E)` becomes `((((I op E1) op E2) op ... ) op En)`

```
template<typeanem ... Args> 
in sum(Args&& ... args)
{ 
	return (args + ... + (1 * 2));
	// return (args + ... + 1 * 2) where this is in error due to precedence of the operators
}
```
This, above, uses `E op pack` as `args + ...`

```
template <typename ... Ts>
auto sub1(Ts ... nums)
{
    return(nums - ...);
}

template <typename ... Ts>
auto sub2(Ts ... nums)
{
    return(... - nums); // outed -13 due to the left associativity
}

int main(){
    std::cout << sub1(1, 2, 3, 4, 5) << std::endl; // outs 3
    std::cout << sub2(1, 2, 3, 4, 5) << std::endl; // outs -13
```

Here's a good example of how the associativity works: 
If we have `(((1 - 2) - 3) - 4) -5 = -13` This is due to to the left associativity, meaning the left goes first, this is really important when we have to figure out how the op's associativity is, remember that C++ won't be able to figure that out just by the by, however, ops do have connected associativity, this one is non-terminal, however, still very very very important. 
`1-(2-(3-(4-5))) = 3` there we have right associtivity. 
Obviously for `+` it doesn't matter, but what happens if we have something that that needs to go first, like a particular expression, let's say `std::cout`

```
template<typename ... Ts> 
void printAll(Ts ... args)
{ 
	((std::cout << args << " "), ...);
}
```
This is a trickier one of course. 
Think about how this expands, here we are expanding over the comma operator, which works sort of differently, then then ones above here. 
There is a feel to it and how it will expand. 

```
(std::cout << 1 << " ", (std::cout << 2 << " ", (std::cout << 3 << " ")));
```

This is what the expansion expands to, notice how you were right, however, our output here woudl still be 1 , 2, 3. 
But isn't the right hand `std::cout` in brackets, shouldn't that go first, of course, **but** 

##### Right Associativity vs Execution Order
What is right associativity: 
`a, (b , c)`
This grouping will indeed affect how the result of the expression is evaluated and combined. 

However, the order of evaluation here is still a , b , c. 
**This is because of the comma operator, in which, C++ mandates that, for this operator (,), the left hand side is always evaluated before the right hand side**. 
There is the comma as an: 
- operator
- separator
- used in semicolons

```
template <typename ... Ts> 
void doAll(Ts ... funcs)
{ 
    ((funcs()), ...);
}

void printOne()
{ 
    std::cout << "ONE" << std::endl;
}

void printTwo()
{ 
    std::cout << "TWO" << std::endl;
}

void printThree()
{ 
    std::cout << "THREE" << std::endl;
}

int main(){ 
    doAll(&printOne, &printTwo, &printThree);

/* 
	ONE 
	TWO 
	THREE
*/
```

Again, notice the order, here the `,` operator is doing work, within that outer bracket, we are expanding over the comma operator, meaning that we have to access left to right, regardless of any brackets. Other binary operators, don't do that, such as `+` which will look at brackets first. 
The comma operator has lower precedence than other operators. 
The associativity is always left to right. 
Regardless of any parentheses around any expression, **it will still evaluate the leftmost expression first**. 

```
int main() 
{ 
	int a = 1, b = 2, c = 3;
	int result = (a + b, c);
	
	std::cout << "Result: " << result << std::endl; // output: 3
}
```

This should be considered when we look at templating and fold expressions. 

```
template <typename ... Ts>
void printAll(Ts ... args)
{
    (std::cout << ... << args);
}

int main(){
    printAll(1, 2);
```

This one should be more obvious: 
```
((std::cout << args1) << args2); 
```
Now of course, the `std::cout` needs to be called first in order to make the `<<` operator work on the others. 
`(std::cout << args << ...)` This won't work, as the right associativity means that the expression unfolds with the `std::cout` last! Without our comma operator to make sure left to right is going smoothly, then we are in a pickle. 


```
template <auto INC, typename ... INS> 
void incrementAll(INS ... args)
{ 
	(print(args + INC), ...);
}

incrementAll<10>(1, 2, 3, 4, 5); // 11, 12, 13, 14
```

Remember that `auto` will decay, however, `decltype` will not decay. 

This next one is a deduction guide, telling the compiler how to deduce when array is given these arguments. 

```
template<typename T, typename ... U> 
array(T, U...)
-> array<std::enable_if_t<(std::is_same_v<T, U> && ...), T>, (1 + sizeof...(U))>;
```

Trailing Return Type: 
This is the part that tells the compiler what we should deduce given the arguments.; 

Remember that the `std::enable_if` can be in the template parameter, in the return type, in the function parameter itself. 

`std::enable_if<(is_same_v<T, U> && ...), T>`
The first gives us a condition that we put into the `enable_if`. 
`(is_same_v<T, U1> && is_same_v<T, U2> && is_same_v<T, U3>)`

Remember this, `args` actually has the values, and the ... tells us which way to associate. 

`args` is a collection of values. Then `...` is used to expand that parameter pack and apply and operation over each element. 
`... << args` this means that `<<` is applied left to right across all the arguments. 
so `(((first arg) << second arg) << third arg)`. 

```
template <typename ... Ts> 
void coutAll(Ts ... args)
{ 
	(std::cout << ... << args) << " "; // left fold
}

// meaning that the space will come at the end. 
```

`((std::cout << args << " "), ...)` 
The comma operator, remember that this will evaluate every single one, but discard values except for the last one. 
This means that we can write something like this: 
`(std::cout << "Hello ", std::cout << "World")` and it will compile and write out "Hello World". 
The comma operator here means that we are unfolding `(std::cout << args << " ")` for every argument about the comma operator. 



