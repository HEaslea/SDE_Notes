When we want to update something in our game. However, we don't want to recompile just to change a few numbers of a few spells etc. 
Then, if we want to mod, or even let players use their own spells, then we have to figure that out too, without running into a whole host of recompiling over and over. 

We need to be able to sandbox things from the core game. 
They need to be separate from the rest of the executable. 

If we can save that file and load it into the game, without changing the executable, then we win. 

### The Interpreter Pattern
The pattern that has the data ready, and uses it to execute something. Interprets the data and uses it. 

```
class Expression 
{ 
public: 
	virtual ~Expression() {}
	virtual double evaluate() = 0;
};
```
Then we can have: 
```
class NumberExpression : public Expression 
{ 
public: 
	NumberExpression(double value) : value_(value) {}

	virtual double evaluate() { return value_; }

private: 
	double value_;
};
```

The evaluation, as this is just a number, that we are taking in, will just evaluate to the same number that it is. 
Addition and multiplication are a bit more complicated: 
```
class AdditionExpression : pulbic Expression
{ 
public: 
	AdditionExpression(Expression* left, Expression* right)
	: left_(left), right_(right) {}


	virtual double evaluate()
	{ 
		// Evaluate the operands
		double left = left_->evaluate();
		double right = right_->evaluate();
		return left + right;
	}
private: // obvious private members here
};
```

The thing here is just setting up the right objects, and wiring them up the right way. 

If we load in data however, we are asking a lot of the size of the program, we are asking a lot by way of wiring it all up properly. 
The instruction and data cache is pretty large here now, just for a simple expression, it might be easy to execute, however, what is the cost. 
The cache will be talked about later as well (on pg. 269). 

So think about it like this: 
### Machine Code, Virtually
When we want to run a game, fast, we create a the code ahead of time, we compile. 
- It's dense, solid contiguous blobs of binary data, no bit goes to waste (efficient).
- Linear , one instruction is executed one after another. No jumping around randomly through memory.
- Low level as each instruction does a relatively minimal thing, interesting behaviour comes when we string them all together. 
- IT'S FAST. 

We can't have users creating machine code for every last thing that they want to change. 
We need a compromise. 

Let's call this little emulator a **VIRTUAL MACHINE**. 
The code that it will rune is **BYTECODE**. 

Lua is similar to this, they implement this pretty simply. 

********
### The Pattern Itself
Remember that we can have an instruction set. 
Meaning that we have the bits, linearly, our virtual machine will run them one at a time. 
We will use a stack for intermediary values. 

Essentially we are writing our own machine code, in reverse, with high level language, to write low level language, to emulate high level language that we don't want to code ourselves, due to the egregious levels of memory. 

This is the most difficult pattern in the book apparently. 

It's creating a language within a language. 

One of the issues is that we say "it will be tiny", and then it grows and grows and eventually we don't know what to do with it. 
Solve this, and you solve everything, you are creating a small, fast, perfectly extensible, system that we can always rely on, you've then cracked the code. 

The idea is too, that you will have some way to generate this bytecode, meaning that users don't have to write in some half concocted assembly language system in order to add something that you have made. 

If you can't perform this authorship system, then you probably shouldn't use this idea. 

