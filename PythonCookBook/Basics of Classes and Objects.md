This is from the new book. 

At the top is 286. 

Classes encapsulate data and processing.

Influenced by the SOLID design principles. 

S  - Single responsibility principle. A class should have only one clearly defined responsibility.

O - Open for extension, closed for modification, extension through inheritance. We shouldn't be tweaking the code in the classes after having written them in order to add/change behaviour.

L  - Liskov Substitution principle, A subclass can be used in place of a superclass.

I   - Interface Segregation principle. Reducing dependencies, decomposing larger problems into possibly many (better to have shorter more explicit and simple classes than a monolith) smaller classes.

D   - Dependency Inversion Principle - Less than ideal for classes to depend directly on other (concrete classes). It's better to depend on an abstraction, and a concrete implementation class is substituted for the abstract class. 

ALSO:
D   - Don't Repeat Yourself (SRP, just in another form)

Nouns - object
Verbs   - methods

Remember that we always have a `self` in the arguments, for normal methods. 
```
def roll(self) -> None: 
	self.....
```

Same with `__init__`
```
def __init__(sefl) -> None:  # using type hinting is a good idea
	self.faces : tuples[int, int] = (0, 0)
```

Make sure that you are using type hints with vars that are classes as well : `car : Car`. 

The idea with `__init__` is that the memory is allocated for the object via `__new__` and then `__init__` is called just to initialize it. 

Similar in C++ as we have `operator new()` , and then we use the Ctor to initialize it.
The Ctor is equivalent to `__init__`. 
And then Destructor is equivalent to `__del__`. 

Internal states may require type hints to show what kinds of values will be assigned by other methods of the class. 

`faces` here has no initial value, therefore, we need to give the type hint to show what it will be: 
```
def __init__(self, n : int, sides : int = 6) -> None: 
	self.n_dice = n
	self.sides = sides
	self.faces : list[int]
	self.roll_number = 0
```

Type hints for return types. 

```
def __str__(self) -> str: 
	return ", ".join(
		f"{i}: {f}"
		for i, f in enumerate(self.faces)
	)

def total(self) -> int: 
	return sum(self.faces)

def average(self) -> float: 
	return sum(self.faces) / self.n_dice
```

And type hints for the parameters as well: 
```
def reroll(self, positions : set[int]) -> list[int]: 
	self.roll_number += 1
	for p in positions: 
		self.faces[p] = self.RNG.randint(1, self.sides)
	return self.faces
```

Type hints will give us all the right error messages. 

Say that we have a `Counter` object, and we give that object some data.

There are two general strategies for designing classes to store and process data: 
- **Extend** - `Counter` in this case, adding statistical processing. There is a section coming up about extending a built in collection - a list that does statistics recipe. 
- **Wrap** - the `Counter` object in a class that provides the additional features required. This opens up more options: 
	-  Expose the underlying (`Counter`) object
	- Write special methods (magic methods) to make the wrapper appear to also be a collection, encapsulating the `Counter`. 

We **wrap**, and we look at two ways to design a computer-intensive processing: 
- **Eager** implementation computes that statistics as soon as possible. The values become simple attributes. 
- **Lazy** doesn't computer until the value is required via a method function or property. This will be seen in Using Properties for Lazy Attributes. 

To see this in C++:  However, in Python we are going to use properties (apparently). 
```
class EagerExample { 
public: 
	EagerExample() {  // as soon as created, no data present but implied
		std::cout << "Computing intensive data eagerly..." << std::endl;
	}

	// then when we come to use it, the data is already evaluated
	void use(){ 
		std::cout << "Using data." << std::endl;
	}
};

class LazyExample { 
	bool computed = false;
public: 
	void use() { 
		if(!computed){ 
			std::cout << "Computing intensive data lazily..." << std::endl;
			computed = true;
		}
		std::cout << "Using data ... " << std::endl;
	}
};

int main(){
	EagerExample e; // computations happen here, with data implied
	
	LazyExample  l; // Computation DOESN'T happen here
	l.use();        //  Does happen here
}
```

They are essentially the same except for when we want to compute the data. 

```
from collections import Counter
import math

# This looks like a wrapper to Counter
class CounterStatistics: 
	def __init__(self, raw_counter : Counter[int]) -> None 
		self.raw_counter = raw_counter
		// as we are evaluating eagerly
		// the most eager time is when the object is created
		self.mean = self.computer_mean()
		self.stddev = self.compute_stddev()

	def compute_mean(self) -> float: 
		total, count = 0.0, 0
		for value, frequency in self.raw_counter.items(): 
			total ++ value * frequency
			count += frequency
		return total / count

	def compute_stddev(self) -> float: 
		total, count = 0.0, 0
		for value, frequency in self.raw_counter.items(): 
			total += frequency * (value - self.mean) ** 2
			count += frequency
		return math.sqrt(total / (count - 1))
```

Quickly: `*` in vim will search forward for the word under the cursor. And `#` searches backward for the same word
This is similar to using `f` and `F` in that we use `n` and `N` to move in the way of the original search or not. 

There's some cool code in the example here: 
```
data_path = Path.cwd() / "data" / "binned.csv"

with data_path.open() as data_file: 
	reader = csv.DictReader(data_file)
	extract = { 
		int(row['size_code']) : int(row['frequency'])
		for row in reader
	}
data = Counter(extract)
```

Using a dictionary comprehension here, to create a mapping. 
This is provided to the `Counter` class. 
```
stats = CounterStatistics(data)
# then use stats.mean
# and stats.stddev
```

The data here is kept separately in a `Counter` object, none of that data is in `CounterStatistics`. 
We can use the values repeatedly, without a cost every single time. 

The only trouble here is that if the `Counter` changes, then this will render the `CounterStatistics` object's state obsolete and incorrect. 

Remember the difference between Mutable : `list dict set` and Immutable : `int float str tuple frozenset`. 

A few reasons for the immutability of these objects: safer and more simple, and performance optimizations, this means we can safely intern/reuse objects without worrying. As they don't change, we can cache them too. We can use them in hashing, for dictionary keys (which are required to be `const`, otherwise they become a new key/hash). 
**Interning** - reusing immutable objects (like strings or small integers), save memory. 
```
a = "Hello"
b = "Hello"
print(a is b) # True - same object, thanks to interning
```

#### Using typing.Named tuple for immutable objects
Think about having a passive container of data, that doesn't do a lot of computing over that data. 
`list, set, dict`, cover most use cases. 
There is a small problem, the way to access isn't quite as elegant as the syntax for accessing an attribute of an object. 

Using properties for `object.attribute` syntax, instead of doing `object['attribute']`. 

There are two considerations here: 
- Stateless (or immutable)? does it embody attributes with values that never change? eg. a `NamedTuple`. 
- Stateful (mutable)? Are there going to be lots of state changes for one or more attributes? This is the default. An ordinary class is stateful.

`NameTuple` - is an immutable, lightweight class with named fields, like a `Tuple` with but readable attribute access. 

```
from collections import namedtuple

Popint = namedtuple('Point', ['x', 'y'])
p = Point(3, 4)
print(p.x)
```

We can also extend from `NamedTuple`

```
class Card(NamedTuple): 
	rank : int
	suit : str

eight_hearts = Card(rank = 8, suit = '\N{White Heart Suit}')

eight_hearts.rank
eight_hearts.suit

eight_hearts[0]
```
We can use either the name or the position within the tuple. We can use `eight_hearts.rank` or `eight_hearts[0`. 

If we do `eight_hearts.suit = '\N{Black Spade Suit}'` 

Remember that tuples are immutable. 
```
t = (1, 2, 3)
t[0] = 9 # TypeError
```

This is because these ints are immutable

```
t = ([1, 2], 3)
t[0][0] = 99 # this is fine as lists are mutable
```

Here, only the top level tuple is mutable, `lists, sets, dictionaries` within a tuple are mutable. 

To be clear, we are inheriting from `NameTuple` meaning that. 

[Container Documents](https://docs.python.org/3/library/collections.html)

Just quickly on `NamedTuple`: 
The main idea is that we are creating lightweight, immutable data structures that behave like both tuples and classes. 
You get a readable field names instead of tuple indices. 
Remember that tuples are immutable, unless the thing that we are altering in them is mutable. 

```
from typing import NamedTuple

class Point(NamedTuple): 
	x: flaot
	y: flaot

p = Point(1.5, 2.5)

print(p.x)
print(p.y)
```

```
from typing import NamedTuple

class Stats(NamedTuple); 
	mean: float
	median: float
	mode: float

def compute_stats(data : list[int]) -> Stats: 
	from statistics import mean, median, mode
	return Stats(mean(data), median(data), mode(data))
```

Notice that they are very very close to `dataclass`. 

Dataclasses are mutable - however, `NamedTuple` are imutable by default. 
`NamedTuple` are slightly more memory efficient. 

```
class Person(NamedTuple): 
	name : str
	age : int

from dataclass import dataClass

p = Person("Alice", 30)
print(p.name) # Alice
# p.age = 31 # Error: NameTuples are immutable

@dataclass
class Person: 
	name : str
	age : int

p = Person("Alice", 30)
print(p.name) # Alice
p.age = 31 # Fine as dataclasses are mutable
```

Just remember that `tuples` are immutable. 

There are different use cases that will be come abundantly clear when you see them. 

We can add methods to this class definition. 
```
class CardPoints(NamedTuple): 
	rank : int
	suit : str 

	def points(self) -> int: 
		if 1 <= self.rank < 10:
			return self.rank
		else: 
			return 10
```
Notice that this will only work for a few games, otherwise we will have to return a different point returning system. 

There is also the idea that there are classes that do processing, rather than containing a lot of data. 

### Dataclasses for Mutable objects
Here we go. 

We know that there are two types of python objects: 
- Mutable - any state changes to one or more attributes. Then we need to build the class from the ground up, or we leverage the property `@dataclass` decorator, to create a class definition from a few attributes and type hitsn. 
- Immutable - values that will never change. `NamedTuple` is our guy. 

So how do we use the `datablass` decorator. 

```
@dataclass 
class Person: 
 name: str
 age: int
```

```
from dataclasses import dataclass

@dataclass 
class CribbageHand: 
	cards : list[CardPoints] # due to the uniqueness, we could also use a set[CardPoints]

	def to_crib(self, card1 : CardPoints, card2 : CardPoints) -> None: 
		self.cards.remove(card1)
		self.cards.remove(card2)
```

Notice that we don't need an `__init__` with this decorator, as we define the class, the `@dataclass` will do that for us. 

`self.cards` is the only instance variable. Make sure that we have the type hint. 

The `dataclass` uses the type hints, therefore we need to use them, they're also just really good practice. 

A very sophisticated `__init__` method is created. 

There is also a number of options that help us choose what features we want in the class.

all are `True` by default. 

Say that we want to use a customized `__init__()` method. 

This means that we need to pass `init=False` to the `@dataclass` . 

Also if we want to define a class-level variable that is not used to create instance variables, that remains a class level variable. 
This is done with the `ClassVar` type hint. 
This indicates a class level variable that is not part of the instance variables or the `__init__()` method. 

```
import random

from typing import ClassVar 

@dataclass(init=False)
class Deck: 
	SUITS : ClassVar[tuple[str, ...]] = (
	'\N{Black Club Suit}', 
	'\N{White Diamond Suit}', 
	'\N{White Heart Suit}', 
	'\N{Black Spade Suit}'
	)

	cards : list[CardPoints]

	def __init__(self) -> None: 
		self.cards = [ 
			CardPoints(rank=r, suits=s)
			for r in range(1, 14)
		]
```
Remember about variable shadowing. 
Shadowing - when we have a variable or an attribute in the local scope that hides a variable or attribute with the same name in an outer scope. 

Object attributes will shadow those of the class itself. 

Therefore, there really is no need to have a static variable, we can assign in the class, that is now static as we can do `Class.variable_a`. Then we also have a new `var` if we have changed it. 

Take for instance: 
```
class Test: 
	var : int = 5

	def __init__(self, a : int): 
		self.var = a

def greet() -> None: 
	print("Greetings")

print(Test.var) # This will be 5
t = Test()
print(t.var)    # This will be 10

Test.greet = greet

Test.greet() # Greetings will be printed

# t.greet()  won't work as it will pass self in by definition
```

However, if `greet` was 
```
def greet(*args): 
```
Then that will work, which actually is a gre-a-t thing. 

### Using Frozen Dataclasses for Immutable Objects
Remember that we use `NamedTuple` in order to define a class that has a fixed set of attributes. 
Remembering that `tuple` are immutable objects. 

As `NamedTuple` is essentially a tuple, we should have a greater ability to fine-tune the behaviour of the instances in this class. 

Some things of the class should not be changing. 

```
from dataclasses import dataclass

@dataclass(frozen=True, order=True)
class Card: 
	rank : int
	suit : str
```

We can now not change the `rank`. 

If we do try to change anything, we will get a `dataclass.FrozenInstanceError`, to let us know that that kdin of operation is not permitted. 

Remember that the `@dataclass` decorator adds a number of built-in method to a class definition, we can disable (as most are enabled by default), a number of different features. 

Think about having a hand that we can add cards to during the game, incrementally.
`field()` will provide a function to build default values, called `default_factory`.

Like so: 
```
from dataclasses import dataclass, field

@dataclass(frozen=True, order=True)
class Hand: 
	cards : list[CardPoints] = field(default_factory=list)
```
The `field()` function provides a default factory : in the event no initial value is provided, `list()` will therefore be used to create a new, empty list. 
![[Pasted image 20250731194456.png]]

The `Hands()` type expects a single attribute, matching the definition of the attributes in the class. 

```
crib = Hand()
d3 = CardPoints(rank-3, suit='\N{WHITE DIAMOND SUIT}')
h.card.remove(d3) # this was created for us
crib.cards.append(d3)
```

![[Pasted image 20250731195025.png]]

### Optimizing Small Objects with `__slots__`: 
Defining an object with a fixed number of attributes, but the values of the attribute can be changed, this is the middle ground between`tuple` or the `NameTuple` that we talked about earlier. 

Moving through, how can we make optimized classes with a fixed set of attributes. 

Generally, as you know, Python will allows adding attributes to an object. 

This can be undesirable, when working a with a large number of objects. 
`__slots__` will limit the class to the named attributes, therefore we cannot add any more. 

Essentially they are a way to make classes more memory-efficient, by telling Python exactly what attributes instances will have. 

By  definition, each object has a `__dict__` to store its attributes: 

However, when we use `__slots__`, `__dict__` will not be made. 

Attributes will be stored in a **static, fixed-size structure** (like a C struct). 
This is faster attribute access and less memory is used. 

However, we would not be able to add new attributes beyond what's declared in `__slots__`. 

The code might look like this: in relation to the book talking about another card game, Cribbage: 
```
class Cribagge:
	__slots__ = {'deck', 'players', 'crib', 'dealer', 'opponent'}

	def __init__(
		self, 
		deck : Deck, 
		player1 : Player, 
		player2 : Player
	)-> None: 
		... the usual stuff here
```

Then you can add methods underneath here too. 

When we create n object instance, the steps in the process are defined in part by the object's class and the built-in `type()` function. 

Implicitly, a class has a special `__new__()` method that handles the internal house-keeping required to create a new, empty object. 
After that, then `__init__()` method creates and initializes the attributes into that memory. 

There are a three essential paths for creating instances of a class: 
- The default, defined by the built-ins `object` and `type()`, when we define a class without doing anything unusually. Each instance contains a `__dict__`, holding all the other attributes in a dictionary structure. This does require the additional memory for the dictionary object inside each instance. 
- The `__slots__` behaviour avoids creating that extra overhead `__dict__`.  There are only the attributes in the `__slot__` sequence, we cannot add or delete attributes. We can change the values, of course, the lack of flexibility means that we are using less memory, there is always a trade-off. 
- The subclass of `tuple` behaviour defines immutable objects. The easy way of creating them is to use `typing`: `NamedTuple` as a parent class. Remember that `tuple` s are immutable, therefore, they would be copied when we try and change something. While it is possible to just subclass `tuple`, the extra features `NamedTuple` seem to make this even more ideal. 

Remember the idea of interning as well: 
```
tup = (1, 2, "Hello there")
f = (1, 2, "Hello there")

print(f is tup) # will print True

tup[0] = 5 # will get an error, 'tuple' does not support item assignment
```

`__new__()` can be changed, however, that is going to be much harder, requires knowing the ins and outs of python. 

Metaclasses are used to create an instance of a class. The default is that of the `type` class. 

The idea being that classes are created by other classes, known as metaclasses. 

When we do: 

```
class Dog: 
	pass
d = Dog() # creates an instance of Dog
```
In order to create dog, Python will `type(Dog)`. Therefore, `Dog` is an instance of `type`. 
This means that `type` is the metaclass. 
The idea being that objects are created by their class, classes are created by their metaclass. 

##### In More Depth
We have to think about the way that the change of creation goes in. 
Remember that everything is an object in Python. 
Therefore when we have the same code that we have above. 
`Dog` is also an **object** in Python. 
So then who creates the `Dog` object. 

Whenever we want to define a class, then we do that by calling the **metaclass** `type`. 
`type` is a class, whose job is to create other classes. 
Under the hood we have `Dog = type("Dog", (), {})`

Which is equivalent to 
```
class Dog: 
	pass
```

The idea is that we just move this one step back to where we say: we are making a class in memory, then we making objects using that class in memory. 

Think of it like a class template in C++, the job is to write code in order to make classes using `template <typename T>` 
```
object ← d (instance)
    ↑
Dog ← a class (instance of type)
    ↑
type ← a metaclass (creates Dog)
```

We can even define a metaclass by inheriting from `type`. 

```
class MyMeta(type): 
	def __new__(cls, name, bases, dct): 
		print(f"Creating class {name}")
		return super().__new__(cls, name, bases, dct)
```

Then we can use it: 
```
class DAog(metaclass=MyMeta): 
	pass
```

When Python sees this, it runs: 
`Dog = MyMeat("Dog", (object,), {...})`

There is a difference between the class templates, however, they both behave very differently. 

##### The differences
**Metaclasses** control how classes how a class, itself is created. We can inject methods, validate class contents, register classes etc. etc. And operates at the class level. 

However, Class Templates allow us to make a series of classes based on various types, no injections etc.
They are all going to be used at compile-time for type flexibility. 
```
template <typename T> 
class Box{ 
public: 
	T value;
};

Box<int> b1;
Box<std::string> b2;
```

Python does all of its stuff at run time, allowing for custom logic (validation, injection). However, C++ only allows this at compile time, based on the uses throughout the source code. 
Output of Python -> modified classes, but in C++, templates just output new classes per instantiations, based on type substitutions. 
These python metaclasses are dynamic/reflective. C++ templates are static/generic.

Think of it as thought metaclasses are like class factories, and templates are blueprints/molds, that generate many versions. 


------
#### Quick Note on Decorators
Remember that a decorator in Python is a like a wrapper, where we have a function that takes another function as a argument, in order to extend the behaviour. 
```
def my_decorator(func): 
	def wrapper(*args, **kwargs): # taking in a certain amount
		print("Before the function runs")
		result = func(*args, **kwargs) # unfold them all and pass them in
		print("After the function")
		return result
	return wrapper # returning the function here

@my_decorator
def say_hello(name): 
	print(f"Hello, {name}")


# gives us this
say_helo = my_decorator(say_hello) # <- This is what @my_decorator does
```

So what about class Decorators/Wrappers

They work similarly: 
```
@decorator
class MyClass : pass

# equivalent to
MyClass = decorator(MyClass) 
```

eg. 
```
def add_greet(cls): 
	def greet(self): 
		print("Hello")
		print("World")
	cls.greet = greet
	return cls
```

We are essentially doing this below when we are defining this class. 

Remember that in Python, you can add functions to a class even after it's defined, which is called **Monkey Patching**: 
```
class MyClass: 
	pass
	
def say_hello(self): 
	print("Hello")

MyClass.say_hello = say_hello

obj = MyClass()
obj.say_hello() 
```

Class decorators are only called once, so that we can add and do things with the class, **at the time that they are defined**. 

**NOT every time that we create a new instance**. 

Remember: 
```
@my_decorator
class MyClass: 
	pass

# is equivalent to this
class MyClass: 
	pass
	
MyClass = my_decorator(MyClass) # This happens at definition time, once
```

This time, however, we are not wrapping in the same manner, and we only need a function that takes in the class that we are passing.

In this case `dataclass` might look a little like this. 

```
# this is the decorator for the class
def dataclass(cls): 
	# inspect the class to find all the fields
	annoations = getattr(cls, '__annotations__', {})

	# generate the __init__
	def __init__(self, **kwargs): 
		for name, typ in annotations.items(): 
			setattr(self, name, kwargs.get(name))

	setattr(cls, '__init__', __init__)

	# Generate __repr__
	def __repr__(self):
		values = ', '.join(f"{name}={getattr(self, name)!r}" for name in annotations)
		return f"{cls.__name__}({values})"

	setattr(cls, '__repr__', __repr__)

	return cls
```

A very simple decorator:

```
def print_dec(cls):
    print(f"Decorating Class : {cls.__name__}")

    original_init = cls.__init__

    def new_init(self, *args, **kwargs): 
        print(f"Creating instance of : {cls.__name__}")
        original_init(self, *args, **kwargs)
        print(f"Instance of {cls.__name__} created")

    cls.__init__ = new_init
    return cls

@print_dec
class Person: 
    def __init__(self, name, age): 
        self.name = name
        self.age = age
    

p = Person("Alice", 30)
```

Here  we are actually changing the `__init__` of the class. 
The key part here is that without even using `p = Person("Alice", 30)`, we will still get the `"Decorating class Person"`, then as we have changed the `__init__`, when we make a `Person` object, that's when we get
