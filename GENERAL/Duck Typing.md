A theme found within dynamically types languages, where the type or class, doesn't really matter, or is less important than the methods it supports and how it behaves. 

> "**If it looks like a duck, quacks like a duck, then it's probably a duck**". 

This is common in python/ruby:
```
class Duck: 
	def quack(self)
		print("Quack!")
		
class Dog: 
	def quack(self)
		print("Woof, i'm pretending to be a duck")


def make_it_quick(animal): 
	animal.quack()

duck = Duck()
dog = Dog()

make_it_quack(duck)
make_it_quack(dog)
```

Here they both output, meaning that the function `make_it_quack` doesn't care whether the object passed is of type `Duck` or `Dog`. 
The only thing that we need here is that `quack` method. 

There is a level of flexibility here, and reusable code, if something behaves in the right way, then it's the same thing as something else, that may be another type, our types don't really matter in python, as long as something acts in the right way. 

There are of course, the initial thought that we had, which was that what if the type that we do pass in, thinking it is a duck, isn't actually a duck and doesn't have the methods of a duck as well. 
