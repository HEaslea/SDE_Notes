Again the C++ Software Design book does a fantastic job at going over these. 

- Single Responsibility Principle (SRP)
- Open-Closed Principle
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
- Dependency Inversion Principle (DIP)

### SRP
When we define a class, or truthfully a function a too, it should only exist for one reason, and be responsible for one aspect of the functionality. 

Each class should have only one job/responsibility, and that job should be encapsulated 
Focused, cohesive and specialized classes. 

We have a better understanding of our code as well. 

Leading, mostly, to smaller, more focused classes, which can be combined, rather than having one very large class that does many things confusingly. 

This is not about minimizing the number of lines of code in a class but rather about ensuring that a class has a single reason to change. 

Although the demonstration is not necessary, I want to write more Python, therefore. 

```
class Report: 
	def __init__(self, content): 
		self.content = content

	def generate(self): 
		print(f"Report content: {self.content}")

	def save_to_file(self, filename): 
		with open(filename, 'w') as file: 
			file.write(self.content)
```

There are a couple of responsibilities in here, the interface does not provide for us a clear idea of what this class does. 
We know that it `generate`s and then `save_to_file`  which is close enough to a little bit of everything. 

We could easily separate this out into two, more concise classes. 

```
class Report: 
	def __init__(self, content : str): 
		self.content : str = content

	def generate(self): 
		print(f"Report content : {self.content}")


class ReportSaver: 
	def __init__(self, report : Report): 
		self.report : Report = report

	def save_to_file(self, filename : str): 
		with open(filename, "w") as file: # context manager
			file.write(self.report.content)
```

As you can see that `ReportSaver` - will take in a `Report`. 
`Report` has `generate` and then `ReportSaver` will do the actual saving to file. 

This is increasing code size, however, this is part of Pythonic Zen, in that the code is simpler, more explicit, more obvious to the immediate eye. 

### OCP
Open-Closed Principle. 

Emphasizing that software entities should be open for extension (common) but closed for modification.
Once we have defined an entity and implemented it, it should not be changed to add new functionality. 
Instead we should extend it through inheritance or interfaces to accommodate new requirements and behaviours. 

Changing and modifying an entity introduces a risk of breaking some other part of the code base relying on it. 

Consider this: 
```
class Rectangle: 
	def __init__(self, width : float, height : float): 
		self.width : float = width
		self.height : float = height

def calculate_area(shape) -> float: 
	if isinstance(shape, Rectangle): 
		return shape.width * shape.height
```

If we were going to add more shapes, then we would have to `calculate_area` function. 
It's not ideal that we have to keep coming back and changing the same part of the code. 
THIS PART IS IMPORTANT: 
> If we repeatedly come back to the same area and change the code there, then we are asking for trouble. If we are repeatedly changing the functionality of some code, to accommodate changes or additions to the code, then we are asking for trouble. 

First, we are going to use a `protocol`, whenever you see a `protocol`, we should be thinking about the structural interface of a class. 

eg. 
```
from typing import Protocol

class Flyer(Protocol): 
	def fly(self) -> None: ... 

class Bird:  # is a Flyer through structure, not inheritance
	def fly(self) : print("Flap!")

def launch(obj: Flyer): 
	obj.fly()
```

To show off OCP: we define a `Shape` protocol: 
```
class Shape(Protocol): 
	def area(self) -> float: ... 
```

Then any class that defines `area(...)`  will be a `Shape` structurally. 

```
class Rectangle: 
	def __init__(self, width : float, height : float): 
		self.width : float = width
		self.height : float = height
		
	def area(self) -> float: 
		return self.width * self.height
		
# This conforms to the Shape protocol

class Circle: 
	def __init__(self, radius : float): 
		self.radius : float = radius

	def area(self) -> float: 
		return math.pi * (self.radius**2)
```


Then we can have a `calculate_area(shape : Shape) -> float` without having to keep adding to it, it does rely on the contract with the `Shape` protocol -> the `Shape` protocol defines the `area()`, therefore we know that when a `Shape` is argued in, we can call `Shape.area()` safely

Therefore we have: 
```
def calculate_area(shape : Shape) -> float: 
	return shape.area() # knowing the shape protocol
```

This is now very clear, that when we add a new shape, we define `area()` in it, then it is structurally a a `Shape` as per the `Shape` protocol. 
Then we know, that when we pass in a `Shape` by definition, it will have defined `area()`, and we can use that. 

This is very clear in C++ too, using a similar line: 
```
class Shape 
{ 
public: 
	virtual void draw() const = 0; // doesn't change anything about this object
	virtual ~Shape() = default;    // always remember the virtual destructor
};

class Circle : public Shape
{ 
public: 
	void draw() const override
	{ 
		std::cout << "Drawing Circle\n";
	}
};

class Rectangle : public Shape 
{ 
public: 
	void draw() const override
	{ 
		...
	}
};

void render(const Shape& shape)
{ 
	shape.draw(); // Polymorphism
}
```

It's clear that whenever we add a new shape, we just adhere to the way that the shape acts. 

### LSP
Liskov Substitution Principle. 

The C++ Software Design book is pretty terrible here, it does a poor job of explaining what this is. This is mostly because C++ doesn't lend itself to a clear demonstration. 

The general idea is this principle dictates how subclasses should relate to their superclasses. 

If a program uses objects of a superclass, then using the subclass should not change the correctness and expected behaviour of the program. 

Put simply, if we have a function that works with the superclass, it should work similarly, and rationally, with an object of the subclass. 

This is very important in large scale systems, where changes in one part can have effects on other parts of the system. 

We need to know that if we extend some class, it will still work in functions. 

Essentially, the subclasses should not be changing functionality radically. 

Consider: 
```
class Bird: 
	def fly(self): 
		print("I can fly")

class Penguin(Bird): 
	def fly(self):
		print("I can't fly") # we might already start to see some issues
```

There is a logical error here in that we are saying `fly` and then the response we get is `I can't`. 

Logically, this has to be improved. 

```
class Bird: 
	def move(self): 
		print("I'm moving")

class FlyingBird(Bird):
	def move(self): 
		print("I'm flying")

class FlightlessBird(Bird): 
	def move(self): 
		print("I'm walking")

def make_bird_move(bird):
	bird.move()
```
