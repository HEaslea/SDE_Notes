```
public class SampleCollection<T>
{
	// Declare an array to store the data elements
	private T[] arr = new T[100];

	// here we are defining an indexer to allow client code to use 
	// [] notation
	public T this[int i]
	{ 
		get => arr[i];
		set => arr[i] = value;
	}
}
```


Here's an example that uses params in order to create an array here:  At least I think that's what params does: 
```
public class ReadOnlySampleCollection<T>(params IEnumberable<T> items)
{ 
	// Declare an array to store the data elements. 
	private T[] arr = [.. items];

	public T this[int i] => arr[i];
}
```

```
public class SampleColelction 
{ 
	private string[] collection = new string[10];

	// Define an indexer property
	public string thisp[int index]
	{ 
		get => collection[index];
		set => collection[index] = value;
	}
}
```
```
// usage
var sample = new SampleCollection(); 
sample[0] = "Hello World"; // set value using index
Console.WriteLine(sample[0]); // Get value using index
```

```
public class Library 
{ 
	private Dictionary<int, string> books = new Dictionary<int, string>();

	// defining an indexer for accessing books by id
	public string this[int id]
	{ 
		get => books.ContainsKey(id) ? books[id] : "Book Not Found";
		set => books[id] = value;
	}
}
var library = new Library();
library[101] = "C# Programming";
Console.WriteLine(library[101]); // output: C# Programming
Console.WriteLine(library[102]); // output: Book not found
```

```
public class Mandelbrot(int maxIterations)
{ 
	public int this[ double x, double y ]
	{ 
		get
		{ 
			var iterations = 0; 
			var x0 = x;
			var y0 = y;

			while(( x * x  + y + y < 4) && 
				(iterations < maxIterations))
			{ 
				(x, y) = (x * x - y * y + x0, 2 * x * y + y0);
				iterations++
			}
			return iterations;
		}
	}
}
```