I believe this is the same in C++ as well: 
#### Expressions : Produce a Value
`x = 3 + 5 # 3 + 5 is an expression`
`print(x * 2) # x * 2 is an expression (evaluates to a value)`

A function is an expression, when it returns a value. 

`y = len("hello") # len("hello") is an expression (evaluates to 5)`

### Statement : Performing an Action
Not returning a value, just doing something: 
`if x > 5: # this is a statement`
`print("Greater") # print() is going to be a statement (does a thing)`

Expressions give us a value, however, a statement does not. 

If what would be an expression, but the return value isn't used, then it becomes a statement: 

```
max(10, 20); // this would be an expression, however it becomes a statement 
// as the return isn't used at all. 
```

### The Walrus Operator
#walrusop
We cannot normally use an assignment expression in a conditional statement. 
However, what if we could have both at the same time. 

Normally an assignment is a statement, so we wouldn't be able to use it in an expression. 

```
if(x = 5): # Syntax Error : assignment is a statement, not an expression
	print("x is 5")
```
Very basically the walrus operator allows for assignment within an expression: 
```
if( x: = 5 ) > 3: # this is fine
	print("x is", x)
```

However, this is not necessary in C++: 
```
int x; 
if(x = 5) // will just evaluate to 5 as x = 5 is done, then x is tested
{ 
}
```
Will evaluate the variable in there, not whether the assignment is successful or not. 

If you're going to do this, then just use parentheses in order to make it super clear: 
```
if((x = someFunction()) > 0)  // clearly an intentional assignment
{ 
}
```

```
if remainder := value % modulus: 
	print(f"Not Divisible! the remainder is {remainder}")
```

The book is saying that for interactive scripts, our users have to choose between some options: 

The code might look something like this: 
```
flavors = [...]
prompt = "Choose your flavour"

print(flavors)
while True: 
	choice = input(prompt)
	if choice in flavors: 
		break;
	print(f"Sorry, {choice}, is not a valid option")
print(f"You chose )
```

However, this is much nicer and more pythonic: 

```
flavors = [...]
prompt ... 
print(flavors)
while (choice := input(prompt)) not in flavors: 
	print(f"Sorry ... ")
print("Good! Flavour picked is")
```
The code is shorter, nicer etc. 
##### Lower Precedence
Therefore we need the () whenever we are going to use it, just making it super clear as well (not sure about precedence in general in python, but this is one of them)

### Building Primes - Only Very Tangential
Sieve of Eratosthenes - I'm in my prime with this one. 
Create an array (`is_prime` this can be a bit vector in C++) of size `n + 1`, all to true (0, 1 to false obviously). 
Then we do this up to $\sqrt{n}$ - as non prime numbers will have at least one factor $\leq \sqrt{n}$ 

```
void sieve(int n)
{ 
	std::vector<bool> is_prime(n + 1, true);
	is_prime[0] = is_prime[1] = false; // 0 and 1 are not prime

	for(int p = 2; p * p <= n; ++p)
	{ 
		if(is_prime[p])
		{ 
			for(int multiple = p * p; multiple <= n; multiple += p)
			{ 
				is_prime[multiple] = false;
			}
		}
	}

	for(int i = 2; i <= n; ++i)
	{ 
		if (is_prime[i]) std::cout << i << " ";
	}
}
```

Think about this, a composite number, that can be factored into two smaller numbers. 
If `n` has factor `a` there must be a factor `b` where `a * b = n`
One of those factors, say `a` must be smaller than or equal to $\sqrt{n}$ , if both were larger, then they would exceed `n`.  

Take 36: 
The factors are: 1, 2, 3, 4, 6, 9, 12, 18, 36. 
Therefore, we only need to do the factors up to 6, as any of the larger factors are going to be products of the smaller ones. 

It's called Sieve of Eratosthenes : after Eratosthenes devised it in 250 BCE, to find all prime numbers up to a given limit `n`. The Sieve part means that we are filtering out non-prime numbers by iterating over multiples of each prime. 

