[Video to get Started](https://www.youtube.com/watch?v=M5HuOZ4sgJE)

# What is Functional Programming

### Programming Paradox
How do humans and machines tackle something differently: 
![[Pasted image 20240329211821.png]]
Most are things that we can't deal very much with: Error Prone, well we could just get better, however, we're not perfect, and we have that edge, it's a positive. 

There is a reconciling of thinking to think more like a machine in some ways: 

A relinquishing of how we have to think about solutions, in the machine world, as a person. 

##### Semantics 
The meaning of a program. 
Does our semantics transfer over to a machine very well, are we just encoding detail of a poor abstraction of the problem? 

#### What is Functional Programming

A style of programming, where the basic method of computation is the **application of functions** to arguments. 

A functional language is one that supports and encourages the **functional style**. 

All languages will have constructs that will allow us to use functional style: 

However, **haskell** is the elephant in the room. 

What does this mean: 
In C family languages the computation method is that of variable assignment. 
However in a more functional style we are looking at function application: in **haskell** we are looking at `function application` eg. `sum [1..10]`, we are not looking at how, but what. 

![[Pasted image 20240329212507.png]]

where as the how is something like this: 
```
int total = 0; 
for(int i = 0; i <= 10; ++i){ 
	total += i;
}
```

What are we trying to accomplish, not about the details.
We don't worry about all the messy bits: 

A solid summary would look something like this: 

> OO (object orientation) makes code understandable by encapsulating moving parts. FP makes code understandable my minimizing moving parts.  

So what does C++ have to offer: 
Contemporary C++ has become more and more functional: 
	Lambdas & closures (the combination of lambdas and capturing variables), std:function, values types and constants, to composability of STL algorithms, lazy ranges, folding, mapping, partial application (bind), higher-order functions or even monads such as optional, future
