Essentially a parameter pack, when we pass it in, we are passing in a variable number of arguments. 

They are treated **as an array** of the specified type, meaning you can pass in multiple values of the same type wihtout explicitly creating an array. 
Sort of like an initializer list: But then definitely like a: 
```
template<typename T>
void variableValues(T ... args)
{ 
	(std::cout << ... << args);
}
```
Essentially, it creates an array, of that type that we pass in. 

Therefore we should write int he array that we are passing in to the params: 
```
void PrintNumbers(params int[] numbers)
{ 
	...
}
```

```
void PrintMessage(string message, params string[] messages)
{ 
	...
}
```
```
void printMessages(string message, params string[] messages)
{
    Console.WriteLine($"{message}");
    foreach(string s in messages)
        Console.Write($"{s} ");
}

printMessages("hello There", "All", "The", "Other");
```

That actually runs, that's so silly. 

```
void PrintItems<T>(params T[] items)
{
    foreach (T item in items)
        Console.WriteLine(item);
}

PrintItems(1, 2, 3, 4, 5);
PrintItems("A", "B", "C", "D");
```

Here's one that uses generics(?): Idk what they are, or how one would use them. 

You can also pass an array explicitly to this: 
```
void PrintNumbers(params int[] numbers)
{ 
	...
}

int[] array = { 1, 2, 3, 4 };
PrintNumbers(array);
```
We can also use this with multidimensional arrays: 
```
void PrintMatrix(params int[][] matrix)
{ 
	foreach(var row in matrix)
	{ 
		foreach(var col in row)
		{ 
			Console.Write(col + " ");
		}
		Console.WriteLine();
	}
}

PrintMatrix(
	new int[] { 1, 2, 3 }, 
	new int[] { 4, 5, 6 }, 
	new int[] { 7, 8, 9 }
)
```
We can even do so with tuples: 
```
void PrintTuples(params (int, string)[] items)
{ 
	foreach(var item in items)
	{ 
		// remember that with tuples you can do this item1 nosense and more
		Console.WriteLine($"Id: {item.Item1}, Name : {item.Item2});
	}
}
```

To NOTE: To save on ambiguity, C# will only allow you to use on `params`, and it has to be last in the param list. 
