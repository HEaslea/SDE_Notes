### Interface
Interface are the rules that we define. 
Declaring methods and properties that a class should provide. 
They usually have pure virtual functions, declared not defined. 
They allow polymorphic properties to emerge more easily. 
Allowing classes to implement the same interface in different ways. 

```
class Shape{ 
public: 
	virtual double area() const = 0; // pure virtual function
	virtual double perimeter() const = 0; // another one
};
```

### Implementation
Referring to the actual code providing functionality declared in the interface. 
Where we find concrete implementations for all the methods declared in the interface. 

```
class Rectange : public Shape{ 
	double length; 
	double width;

public: 
	Rectangle(double l, double w) : length(l), width(w) {}
	double area() const override { return length * width; }
	double perimeter() const override { return 2 * (length + width);}
};
```

`Shape` is the interface defining two pure virtual functions, `area()` and `perimeter()`.
`Rectangle` is implementing
