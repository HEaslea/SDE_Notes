Statements are kept simple in Python. 
Evaluate a function and assignments. 

### Writing Python Scripts and Module Files - Syntax Basics
Creating automated solutions. 
Scripts and Modules -> organize what we have going on. 
A script is a module; however, with an intent to do useful processing when started by the OS. 
#### Creating a Script File
The first line looks like this: 
`#!/usr/bin/env python3`
Setting an association between file you're writing and Python. 

`#!` -  tells the OS how to run the script

`/usr/bin/env` - finds `python3` dynamically in the system's `PATH`

`python3` specifying the script should be run with Python3. 

This makes the script portable over different systems. 
Works is Python3 is in a different location. 

Avoids hard coding a path. 

```
chmod +x script.py # make it executable
./script.py # runw ithout needing python3 script.py
```

##### The Shebang `#!`
Interpreter directive, tells the OS which program should be used to execute a script. 

When the script starts: `#!/path/to/interpreter`

```
#!/usr/bin/env python3
print("hello World!")
```

Or we can do this: 
```
#!/bin/bash
echo "Hello, bash!"
```
Running with Bash. 

Bash, Python, Ruby and Perl use this. 

Mainly for scripting languages

**NOT USED IN COMPILE LANGUAGES**. 

We can use the bash `chmod` command

##### `chmod`
Bash - Bourne Again Shell - Shell->CLI, interact with the OS by typing commands, the translator between the User and the OS. 

Bash - Bourne Again Shell - Common Linux/mac, scripting capabilities. 
CMD (Command Prompt) - Default shell on Windows for running basic commands. 
PowerShell - Advanced Shell on Windows with Object-Orientated Scripting. 
Zsh - An alternative to Bash, better features. 

A Bash command that changes the permission. 
`chmod [options] mode file`
There are 3 types of permissions: 
`r` - read (4)
`w` - write (2)
`x` - execute(1)

`u` User
`g` Group
`o` Others
`a` All

1. Making a file executable : giving execute permission to a script, use: 
`chmod +x script.sh`

`chmod +x script.py` with the SheBang at the top to let the OS know what we are using: 
`./script.py` at the end there, and that will run it all. 

This will make it a first class application, as usable as any of the built-in-commands. 

Then we put in the 
```
"""
A summary of this script
"""
```
The `"""` can be indefinitely long

Then imagine that we have all the code that will do something: 
`print("Hello World")`


```
#!/usr/bin/env python3
"""
My First Script : Doing Something
"""

print(355/113)
```

Remember the Linux loader is something called `execve`, which will look at the first few bytes of the a file to see what the file contains. 

They are sometimes called the `magic bytes`. 
Therefore whatever is after the `#!` -> will process the rest of the data in the file. 

##### Writing Long Lines of Code
Narrower text - easier to read at the cognitive level. 

How do we break up a line into multiple lines - logical lines - using the `\` in order to break up the line
Python has a rule that if we use `()` and `[]` and `{}`. 
As the they have to match, python will look through multiple lines at a time. 
`("a" "b")` is the same as `"ab"`
Or just decompose the statements - make lines shorter and use more variables if you have to. 

```
message_text = f'the internal representation \
{mantissa_whole:d}/2**53*2**{expontent:d}'
```
The `\` must be the last character on the line. 

Using `()` to break up lines too: 

```
import math
example_value1 = (63/25) * (17 + 15*math.sqrt(5) / 
(7 + 15*math.sqrt(5))
```

We can turn into something like this: 
```
example_value3 = (63/25) * (
	(17 + 15* math.sqrt(5))
	/ (7 + 15 * math.sqrt(5))
)
```

There are logical lines and physical lines. 
Logical line - contains a complete statement, and **can span multiple physical lines**. 
Explicit and Implicit line joining - `\` explicit line joining, however, not generally encouraged. 
`()` for implicit line joining in many cases. 
```
print("Hello there!"
	"How are you doing")
```

We might have something like this:
```
from math import ( 
	sin, cos .... 
)
```

#### Descriptions and Documentation

- Library Modules - Mostly function definitions, and class definitions as well. The docstring should describe what this module does eg. how to use the functions, and the classes. 
- Scripts - what we would generally expect to do some real work. The docstring should describe what it does and how to use it. The options, environment variables and config files are important parts of the docstring. 

**Docstrings for Scripts**: 
using RST - ReStructuredText Markup. 
`==================` to separate things etc. 

![[Pasted image 20250308171619.png]]![[Pasted image 20250308171630.png]]
Then very similar for module libs. 

#### Sphinx Python Documentation Generator
I would look into this much more. 

Just remember as well, that there is a lot to be said for RST - ReStructured Text Markup - just like markup in obsidian - there are rules to that. 

![[Pasted image 20250308172319.png]]

The underlines are used to organize the sections into a properly nested hierarchy. 

![[Pasted image 20250308172504.png]]

#### Designing Complex if...elif Chains
**The Law of the Excluded Middle**: 
Imagine that we have something like this: 
```
if weather == Weather.RAIN and plan == Plan.GO_OUT: 
	bring("Umbrella")
else: 
	bring("sunglasses")
```
It may not be immediately obvious, but we have omitted a number of possible alternatives. 
There are 4 variations and combinations for these two. One is stated the others are assumed. 

The general idea is that when we write an `if` statement we have to make sure that all the bases are covered. 

I think what the chapter is trying to say is that write all the `elif` to be covering all the bases, and then the `else` to cover any logical problem. 

Called the **Else-Raise**: Meaning that we are explicit with every condition, nothing is assumed. 

Something as simple as: `m = max(a, b)`

Might look like this with the `else-raise` ideology. 
```
if a >= b: 
	m = a
if b >= a:  # making it super obvious what we are doing here
	m = b
else: 
	raise Exception('Design Problem')
```

Think about the universe of conditions here $U = \{ a \geq b, \; b\geq a \}$. 

##### Saving Intermediate Results With the `:=` "walrus" operator
Preserving an an expensive intermediate result. 
See this summation: $\sum_{0 \leq n < \infty} (\frac{1}{2n + 1})^2$
As `n` grows, then we square, eventually that's so small that we are adding very very small values. 

`s = sum((1 / (2 * n + 1)) ** 2 for n in range(0, 20_000))`
What if only 16000 is enough to make an accurate value. 

```
b = 0
for n in range(0, 20_000): 
	if(1 / (2 * n + 1)) ** 2 >= 0.000_000_001:
		b = b + (1 / (2 * n + 1)) ** 2
```
This is constantly repeating an expensive computation `(1/ (2 * n + 1)) ** 2`. 

First isolate the expensive part of the operation. 

```
p = 0
for n in range(0, 20_000): 
	term = (1 / (2 * n + 1)) ** 2
	if term >= 0.000_000_001: 
		p = p + term
```
Rewrite the assignment state to use the `:=`. This replaces the simple condition of the `if` statement. 

```
q = 0
for n in range(0, 20_000): 
	if(term := (1 / (2 * n + 1)) ** 2) >= 0.000_000_001:
		q = q + term
	else: 
		break
```

Basically it allows you to assign to a variable within an expression: 

Here's another example in the middle of the others: 
```
line = input("Enter Text: ")
while line != "quit": 
	print(f"You entered: {line}")
	line = input("Enter text: ") 
```
That could look like this with the `:=` operator: 
```
while (line := input("Enter Text: ")) != "quit":
	print(f"You entered: {line}")
```

Another: 
```
match = re.search(r"\d+", "abc123xyz")
if match: 
	print(match.group())
```
```
if(match := re.search(r"\d+", "abc123xyz")): 
	print(match.group())
```

The `:=` will save an intermediate result. 

`a + (b := c + d)` is the same as `a + (c + d)`. 

Assignment operations can be most anywhere, only a few places is it forbidden. 

Assignment within an expression. 

```
if(n := len("Hello")) > 3: # Assign len("Hello") to n and checks condition
	print(f"Length is {n}") 
```

This means that we don't have to do reassign and re-evaluate some extreme expression or calculation. 

Very useful in loops. 

```
# so that we don't have to use something like this
value = input("Enter: ")
while value != "exit": 
	print(...)
	value = input("Enter: ")

# turns it into this
while(value := input("Enter: ")) !=  "exit";
	print(f"You entered: {value}")
```

Remember that in C++ you can use the comma operator, that gives us a similar example: 
```
int x;
if((x = 10), x > 5) 
{ 
	// always true
}
```

#### Avoiding Problems with `break` Statements
Imagine that we have a large collection of data and we use `for`, we are saying that `for all`, then if we use a break, we are saying `there exists`, such an element that would break the loop. 

The main idea is that we want to minimize the conditions in a `for` loop, and the conditions for leaving one as well. 

Say that we want the value after either a `:` or `=`: 

```
for position in range(len(sample_1)): 
	if sample_1[position] in '=:':
		break

print(f"name={sample_1[:position]!r}", 
	f"value={sample_1[position+1]!r}")
name='some_name' ' value=' the_value'
```

```
sample_2 = "name_only"
for position in range(len(sample_2)): 
	if sample_2[position] in '=:': 
		break;
print(f"name={sample_2[:position]!r}", 
	f"value={sample_2[position+1:]!r}")
name='name_onl' value = ''
```
Here, you can see we've dropped the `y`, oh dear. 

We need to nail down what should be true after the `for` or `while` statement. 

So here, if there is a `:` or `=`, and if there isn't. 

There are the edge cases: 
- There's no `=` or `:`
- There are no chars at all, giving us `len() = 0`, and the `for` statement never does anything. This does mean, and this is crucial, that the `position` variable is never actually used. 
So how do we incorporate all of these. 
```
position = -1
for position in range(len(sample_2)): 
	if sample_2[position] in '=:':
		break

if position == -1: 
	print(f"name=None value=None")
elif not(sample_2[position] == ':' or sample_2[position] == '='): 
	print(f"name={sample_2!r} value=None")
else: 
	print(f"name={sample_2[:position]!r}",
		f"value={sample_2[position+1:]!r}")
```


Here we are enumerating all the terminating conditions. 

This means that we know every single terminating condition and what happens. 
Meaning that we do not guess. 

Weirdly you can have an `else` clause on a `for` loop. 

```
for position in range(len(sample_2)): 
	if sample_2[position] in '=:': 
		name, value = sample_2[:position], sample_2[position+1:]
		break
else: 
	if len(sample_2) > 0: 
		name, value = sample_2, None
	else: 
		name, value = None, None
```
#### Leverage Exception Matching Rules
```
try: 
	x = 1 / 0 # raises a ZeroDivisionError
except ZeroDivisionError: 
	... 

# or we can do something like this
try: 
	x = int("hello") # raises value error
except (ZeroDivisionError, ValueError) as e: 
	print(f"{e}")
```
Remember that in C++: 
```
try{ 
	throw runtime_error("Something went wrong!");
} catch (const exception& e)
{ 
	cout << e.what() << endl;
}

// or

try{ 
	throw 42; 
} catch(int e){ 
	cout << "Caught an int: " << e << endl;
} catch(...) { // catches all
	cout << "Unknown exception caught!" << endl;
}
```

What can we do when we have an exception: 
- Ignore it: the program may stop - don't use a `try` or don't use a `except`
- Log it: We can let it propagate after writing a log, using the raise statement. 
```
# propagation of exceptions

def func1(): 
	return 1/0 

def func2(): 
	func1() # doesn't catch so it propagates

def main(): 
	try: 
		func2()
	except ZeroDivisionError: 
		... caught here
main()
```

Same thing in C++: 
```
void func1() 
{ 
	throw runtime_error("Error in func1");
}

void func2()
{ 
	func1();
}

int main() 
{ 
	try { 
		func2(); 
	} catch(const runtime_error& e) { 
		cout << "Caught Extension: " << e.what() << endl;
	}
}
```

Both will terminate if we do not catch these. 

- Recover from it: except clause to do some recovery action to undo any effects of the partially completed `try` clause. 
- Silence it: if we do nothing, then processing is resumed after the try statement, however, this doesn't correct the problem. 
- Rewrite it: raise a different exception. 

There's also the idea that an exception could be ignored by the inner `try` but handled by the outer `try`. 

```
def inner(): 
	try: 
		x = 1/0
	except ValueError: 
		print("...")

def outer(): 
	try: 
		inner()
	except ZeroDivisionError: 
		print("...")
		
outer()
```
Can just do the same thing with `C++`. 



