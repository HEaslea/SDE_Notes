Providing a concise way to implement methods, properties, and other members, using an expression instead of a full block of code. 

The expression after the `=>` is the return type

`public string GetName() => _empName;`

The equivalent would be: 
```
public string GetName()
{
	return _empName;
}
```

`public int square(int x) => x * x;`

```
private string _name;
public string Name => _name;

// which is equivalent to a readonly property like
public string Name
{ 
	get{ return _name; }
}
```
Even constructors can be expression bodied: 
`public MyClass(int x) => _value = x;`
Which is equivalent to: 
```
public MyClass(int x)
{ 
	_value = x;
}
```

Not sure what these are yet, however, you could also use them for indexers: 
`public string this[int index] = _array[index];`

And finalizers: which I also don't know what they do yet: 
`~MyClass() => Console.WriteLine("Destructor Called");`
But it looks like they are just destructors.

```
public class Rectangle
{ 
	private int _width;
	private int height;

	public Rectangle(int width, int height) => (_width, _height) = (width, height); // this some next level

	public int GetArea() => _width * _height;
}
```
