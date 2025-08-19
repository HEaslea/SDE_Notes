Inheritance -> extension. 

Functionality through wrapping. 

Therefore wrap vs extend. 

A Python class can inherit features from more than one superclass. 
This is a **mixin** design pattern. 

#### Choosing Between Inheritance and Composition
Chapter 6 - Using `cmd` to  create command line applications. 

The idea of extension is that of generalization-specialization. The `is-a` relationship. 
There's an important semantic issue: 

- Do we mean that subclass instances are also instances of the superclass? As this would really be an is-a relationship, and inheritance, where we extending a class. Changing the implementation details of features. 
- Perhaps there is composition or association, sometimes called a has-a relationship. There is the idea of wrapping another class, adding or removing features. 

Remember the Liskov Substitution Principle : requires any subclass to be a proper replacement for the superclass. 

Remember, too, that often we have an abstract class and then concrete examples. 

```
from typing import NamedTuple

class Card(NamedTuple): 
	rank : int
	suit : str

Spades, Hearts, Diamonds, Clubs = ( # the utf-8 for the symbols)
```

Collections: 
- **Aggregation**: objects are bound into collections, but the objects have properly independent existence. Ie. when the hand object is destroyed, the Cards still exist. 
- **Composition**: When an object does not have independent existence, meaning that it will only exist in the container of it. 
- **Inheritance** : `is-a`. We might say that the `Deck` is a `Hand` with extra features (I personally wouldn't, however, you could say that). The main thing we should really be thinking here is, `Deck` is really an extension of `list`. 

Wrapping, there is the idea of composition and aggregation. 

Writing using the wrapper idea: 
Where we have an iterator for dealing.
```
class deck_w: 
	def __init__(self, cards : list[card])
		self.cards = cards
		self.deal_iter = iter(self.cards)
```

Remember that `iter(obj)` will return the `iterator object` for `obj`. 

Remember that an iterator must implement : 
`iter()` 
- `__iter__(self)` returns itself (the iterator object)
- `__next__(self)` returns the next item or raises `StopIteration`

```
class MyListIterator: 
	def __init__(self, lst):
		self.lst = lst
		self.index = 0

	def __iter__(self): # has to return self
		return self

	def __next__(self): 
		if self.index >= len(self.lst): 
			raise StopIteration
		val = self.lst[self.index]
		self.index += 1
		return val
```

Continuing on: 
```
class deck_w: 
	def __init__(self, cards : list[card])
		self.cards = cards
		self.deal_iter = iter(self.cards)

	def shuffle(self) -> None: 
		random.shuffle(self.cards)
		self.deal_iter = iter(self.cards)

	def deal(self) -> Card: 
		return next(self.deal_iter)
```

```
domain = list( 
	Card(r + 1,s)
		for r in range(13)
			for s in (Spades, Hearts, Diamonds, Clubs);
)
```


See C++ function resolution as well.

Python implements a clever search algorithm for finding methods of an object's class. 
1. Examine the object's class for the method or attribute name. Simple, look in the class first. 
2. If not defined in the immediate class, search in all of the parent classes for the method or attribute. The **Method Resolution Order** (MRO) defines the order in which these classes are searched. In all we look through other areas (parent classes) in order to find the method. 

Search through parent classes ensures two things : 
- All methods defined in any superclass are available to subclasses. 
- Any subclass can therefore override a method to replace the superclass method. The `super()` function searches parent classes for the definition being overridden. 

The potential to override methods by a subclass. 

Some languages (C++ using `final`) have the ability to lock the overriding. 
Python doesn't have this, therefore the subclass can, most of the time override a method. 

We can use `super()` in order to wrap the `super().method()`. 

```
class SomeClass(Parent): 
	def some_method(self) -> None: 
		# do something extra
		super().some_method() # getting the parent version
```

This just means that we can add extra bits without changing the `super()` version of the method. 

When using inheritance, we receive a wealth of method definitions from the superclass. 
This leads to some additional behaviours in the `Deck_X` that may not be desirable.

`list` class has a number of features for converting Python collections to lists. 
As `Deck_X` class is a list, it also works directly with the `iter()` function. 

### Separating Concerns through Multiple Inheritance
Remember that C++ has virtual inheritance in order to avoid this idea of multiple inheritance. 
Given the `Card` object have a rank and suit attributes. 
This creates two small problems: 
- We had numeric ranks and not JG, Q, K. And an Ace as 1, not A. 
- Cribbage assign a point value to each rank, generally the face cards have 10 points. The remaining cards have points that match their rank. 

Multiple inheritance lets us handle variations in card game rules while keeping a single, essential `Card` class. 

This allows us to separate rules for specific games, from generic properties of playing cards. 
Then we have a base class definition, with a mixin class that provides needed features. 

[[Mixins]]

Multiple Inheritance relies on a clever algorithm called **C3** to resolve various parent classes into a single list, in a useful order. 

When combining multiple classes, they will have common parent classes, which would have multiple references, which is the same in C++, that we have to solve with virtual inheritance. 
C3 crates a linear list, that respects all of the parent-child relationships. 

Remember the diamond problem in C++ (realistically this can be any shape, but the diamond is the fastest way to illustrate the issue). 

eg. 
```
class Base{};

class A : virtual public Base {};
class B : virtual public Base {};
class C : public A, public B {}; // ONly one base subobject due to virtual inheritance
```
Similarly Python, uses C3 linearization (MRO : Method Resolution Order). 

Flattening the inheritance graph, into a single consistent order, so the base class appears once in the hierarchy. 

A practical extension to the `Card` class needs to be a mixture of two feature sets. 

Python lets us define a class that includes features from multiple parents. 

Two parts: 
1. **Essential Features**: Rank and Suit. Includes a method to show the `Card` object's value nicely as a string using "J", etc. and "A" for aces. 
2. Mixin Features: Less essential, game-specific features, number of points allotted to each particular card. 

```
@dataclass(frozen=True)
class Card: 
	"""Superclass for cards"""
	rank : int
	suit : str

	def __str__(self) -> str: 
		return f"{self.rank:2d} {self.suit}"

// define a subclass to implement some specializations
// needing two subclasses of the Card class
class AceCard(Card): 
	def __str__(self) -> str: 
		return f" A {self.suit}"

class FaceCard(Card): 
	def __str__(self) -> str: 
		# a dict here to map each number to a str
		names = {11: "J", 12: "Q", 13 : "K"}
		return f" {names[self.rank]} {self.suit}"


# each one overrides the __str__() method
# defining the core features, using typing.Protocol


```

Remember that `Protocol` provides a structural subtyping (static duck typing). 

eg. 
```
class Animal: 
	def speak(self) ... 

class Dog(Animal): 
	def speak(self): print("Woof")


def make_it_speak(a : Animal): 
	a.speak()
```

Here only animals can speak, they have to be an `Animal` and then have the `speak` interface. 
However using protocol, the class itself doesn't matter, just if it has the right methods and interface is what matters. 
We see this in C++ when we use templates in order to take in whatever classes, and `concepts` to minimize those "whatever" classes. 

```
from typing import Protocol

class Speaker(Protocol): 
	def speak(self) -> None: .. 
	
class Dog: 
	def speakf(self): print("Woof")

class Robot: 
	def speak(self): print("Beep")

def make_it_speak(s : Speaker): 
	s.speak()
```

`Dog` and `Robot`  -> work with `MakeItSpeak` as we have a `Speaker` `Protocol`. 

They are basically `concepts` from C++20. 

Similar thing in C++: 

```
#include <concepts>
#include <iostream>

template<typename T> 
concept Speaker = requires(T t)
{
	{ t.speak() } -> std::same_as<void>; // t.speak, that returns void
};

void make_it_speak(Speaker auto& s)
{
	s.speak();
}
```
Similarly : 
```
template<typename T> 
concept SizedLike = requires(T t) 
{ 
	{ t.size() } -> std::convertible_to<std::size_t>; // return can be converted to size_t
};

void print_size(SizedLike auto const& x)
{ 
	std::cout << x.size() << "\n"; // we know the rules that apply here
}
```

Another way of doing it 
```
template <typename T> 
requires requires(T t) { { t.speak() } -> std::same_as<void>; }
void make_it_speak(T& t){ t.speak(); }
```

Moving on to the Python code from the book: 

```
class AceCard(Card): 
	def __str__(self) -> str: 
		return f" A {self.suit}" # get the right string for Ace Cards

class FaceCard(Card): 
	def __str__(self) -> str: 
		names = { 11 : "J", 12 : "Q", 13 : "K" }
		return f" {names[self.rank]} {self.suit}" # get the right string for Face Cards

from typing import Protocol

class PointedCard(Protocol): # we have a Protocol here, essentially  a concept
	rank : int
	def points(self) -> int: 
		...
		
# something is a PointedCard if they implement points, and has a rank attribute

class CribbageFacePoints(PointedCard): 
	def points(self) -> int: 
		return 10

# Creating concrete Classes
# Standard Cribbage Card
class CribbageCard(Card, CribbagePoints):
	pass

# Ace Cribbage Card
class CribbageAce(AcedCard, CribbagePoints):
	pass

# Face Cribbage Card
class CribbageFace(FaceCard, CribbageFacePoints):
	pass

# we have defined some classes here that we are using just for cribbage 

# we have a factory, that returns a class based on the args
def make_cribbage_card(rank : int, suit : str) -> Card: 
	if rank == 1:  # this is an ace 
		return CribbageAce(rank, suit)
	elif 2 <= rank < 11: # not an ace or a face card
		return CribbageCard(rank, suit)
	elif 11 <= rank: 
		return CribbageFace(rank, suit)
	else: 
		raise ValueError(f"invalid rank {rank}")
```

Then usage could be: 
```
import random

random.seed(1)
deck = [make_cribbage_card(rank+1, suit) for rank in range(13) for suit in SUITS]
random.shuffle(deck) # shuffle 
len(deck) # we have 52

[str(c) for c in deck[:5]]
# will output the first 5 cards 0-4, that is 5 cards, lost my head a little here
```

Then we can evaluate the points for these cards: 
`sum(c.points() for c in deck[:5])`

Python's mechanisms for finding a method (or attribute) of an object works like this: 

1. Search the instance for the attribute (Shadowing)
2. Search in the class for the method or attribute (in the actual class)
3. If not defined in the immediate class, then search all of the parent classes for the method or attribute. The sequence of the search is called the **Method Resolution Order** (MRO). 

More: 
1. **Instance Dir** `obj.__dict__`, if the instance has the attribute directly, then use that.
Eg. 
```
class Test: 
	def TestOut() -> None: 
		print("TestOut from Test Class")

def tOut() -> None: 
	print("TestOut from Outer Method")

t = Test()
t.testOut = tOut

t.testOut() # we get TestOut from Outer Method
```

We can use `mro()` method of a class to see the order of the lookup. 

##### Design Concerns
There are a few concerns when we introduce mixins:
- **Persistance and Representation of State** : A mixin class could add methods to manager conversion to a consistent external representation like CSV, or JSON notation. 
- **Security**: A mixin class could add methods performs a consistent authorization check that applies to a number of base classes. 
- **Logging**: Introduce a logger with a definition across a consistent across a variety of classes. 
- **Event Signaling and Change Notification**: 

Creating a mixin to add logging to the idea of the cards we've seen so far. 

