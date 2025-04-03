`var tuple = new Tuple<int, string>(1, "Apple");`
`var` being almost the same as `auto`. 

Accessing into tuples: 
`tuple.Item1`
I think `Item1, Item.2` etc are part of the Tuple class itself

You can also declare as such: 
`(double, int) t1 = (4.5, 3);`
Then there are named Tuples as well: 
`(double Sum, int Count) t2 = (4.5, 3);`
Then we reference the items `t2.Count` etc., seems very obvious. 

Some use Cases: 
```
int[] xs = new int[] {4 , 7 , 9};

var limits = FindMinMax(xs); // this would be 4, 9

// you can output a tuple in such a way
Console.WriteLine($"Tuple: [{string.Join(" ". xs)}]);

int[] ys = new int[] { -9, 0, 67, 100 };
var (minimum , maximum) = FindMinmax(ys); // not really sure what this is yet

// i think this is returning a tuple, which after all 
// is just a mechanism for transporting various output without `out`
(int min, int max) FindMinMax(int [] input)
{ 
	if(input is null || input.Length == 0)
	{ 
		throw new ArgumentException("ERROR");
	}

	var min = int.MaxValue;
	
	var max = int.MinValue;

	foreach(var i in input)
	{ 
		if(i < min)
		{ 
			min = i;
		}
		if (i > max)
		{ 
			max = i;
		}
	}
	return (min , max);
}
```
Another way to create named tuples: 
`var tuple = (Id: 1, Name: "Apple")`

When assigning to a new Variable, we can change the names: 
`(int id, string fruit) tuple = (Id: 1, Name: "Apple");`

##### Deconstructing Tuples
```
var person = (Id: 1, Name: "John Doe");

var (id, name) = person; // here id and name are separate variables
```
```
var newTup = (A: "Hello there", B: 2, C: 3.14);
var (x, y, z) = newTup;

Console.WriteLine($"{x}, {y}, {z}");
```
```
void PrintPerson((int Id, string Name) Person)
{ 
	var(id, name) = person;
	// output here
}

PrintPerson((1, "John Doe"));
```
The names sort of carry as well:
```
public (int Sum, int Product) Calculate(int a, int b)
{ 
	return (a + b, a * b);
}

var result = Calculate(3, 4); 
Console.WriteLine(result.Sum);
Console.WriteLine(reuslt.Product);
```

#### Using Tuples as Dictionary Keys
```
// bit more "difficult" in C++
var dict = new Dictionary<(int, string), string>();
dict[(1, "Apple")] = "Fruit";
dict[(2, "Carrot")] = "Vegetable";
```

##### Mutable Vs Immutable
```
var tuple = (Id: 1, Name: "Apple");
tuple.Id = 2;
// see how this can change
```
However the `Tuple` class is immutable: This class is the older version of tuple
```
var tuple = Tuple.Creawte(1, "Apple");
tuple.Item1 = 2; // would be a compilation error
```

##### Nested Tuples
`var nestedTuple = (Id: 1, Name: "John Doe", Address: (Street: "123 main street", City: "AnyTown"));`


#### Pattern Matching 
```
var point = (X: 10, Y: 20);

switch (point)
{ 
	case (0, 0);
		Console.WriteLine("ORIGIN");
		break;
	case var (x, y) when x == y: 
		Console.WriteLine("X and Y are equal");
		break;
	default: 
		// output whatever you want
		break;
}
```

Using name tuples seems to be the standard. 

### Assignment and Deconstruction
```
(int, double) t1 = (17, 3.14);
(double First, double Second) t2 = (0.0, 1.0);
t2 = t1; 
// this is fine, as the tuples are the same length and the tuples,
// have convertible elements to each other.
```

This is deconstruction, remember that you 
```
var t = ("post office", 3.6);
var (destination, distance) = t;
```

```
var t = ("post office", 3.6);
(string destination, double distance) = t;
// is also a more explicit way of saying it 
```

```
// half and half
var t = ("post office", 3.6);
(var destination, double distance) = t;
```

#### As out Parameters
```
var limitsLookup = new Dictionary<int, (int Min, int Max)>()
{ 
	[2] = (4, 10);
	[4] = (10, 20);
	[6] = (0, 23);
};

if(limitsLookup.TryGetValue(4, out (int Min, int Max) limits))
{ 
	Console.WriteLine($"Found limits: min is {limits.Min}, max is {limits.Max}");
}
```

- `System.ValueTuple` types are [value types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types). `System.Tuple` types are [reference types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/reference-types).
- `System.ValueTuple` types are mutable. `System.Tuple` types are immutable.
- Data members of `System.ValueTuple` types are fields. Data members of `System.Tuple` types are properties.