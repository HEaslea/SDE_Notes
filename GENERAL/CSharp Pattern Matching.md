The basic form is using the is word: 
```
object obj = 42;

if(obj is int number)
{ 
	// do something
}
```

In this one we check to see if `shape` is `Circle` and we give it a new var name `c`
```
object shape = new Circle(); 
if (shape is Circle c)
{ 
	// do a thing
}
```

##### Constant Pattern
```
int number = 10; 
if(number is 10)
{ 
	// do a thing
}
```

#### Relational Pattern
```
int score = 85; 
if (score is >= 90 or < 50) // literally just english wtf
{ 
	 // do a thing
}
```
#### Property Pattern
```
// I think these are properties, not sure though
Employee emp = new Empolyee { Name = "Alice", Age = 30};

if(emp is { Name: "Alice", Age: 25})
{ 
	// do a thing
}
```

#### Positional Pattern
```
var point = new Point(3, 4);
if(point is (3, 4))
{ 
	// do a thing
}
```

### Pattern Matching with `switch`
```
object shape = new Circle(5);
switch (shape)
{ 
	case Circle c: // we rename shape to c
		Console.WriteLine($"It's a circle with radius { c.Radius }. ");
		break;
		
	case Rectangle r:
		Console.WriteLine($"It's a rectangle {r.Width}, {r.Height}");
		break;

	default: 
		Console.WriteLine("Unknown Shape.");
		break;
}
```

```
object shape = new Rectangle { Width = 10, Height = 20 };
if(shape is Rectangle { Width : 10, Height : var h}) // extracting height into h
{ 
	Console.WriteLine($"Rectangle with width 10 and heigh {h}.");
}
```
