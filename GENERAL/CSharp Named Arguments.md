```
void DisplayInfo(string name, int age, string country)
{ 
	Console.WriteLine($"Name : {name}, Age: {age}, Country : {country}");
}

// using named arguments, we can put them in any order
DisplayInfo(age: 25, country: "UK", name: "Bob");
// and a mixture of positional and named arguments
DisplayInfo("Charlie", country: "Canada",age: 25);
```

Don't mix them up too much though, doesn't really work for anyone involved. 
You can't put positionals after named either. 
