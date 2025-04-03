## Read Only
```
class MyClass 
{ 
	public readonly int myReadOnlyField; 

	public MyClass(int value)
	{ 
		myReadOnlyField = value;
	}

	public void Print() 
	{ 
		Console.WriteLine($"...");
	}
}

MyClass obj = new MyClass(10);
obj.Print();
// obj.myReadOnlyField = 20; // This is a compiler error
```
Here we are using `readonly` with a `value_type`: 
#### With Reference_types
When we use `readonly` with reference types, the reference itself becomes immutable, but the contents of the object it points to  can still be modified. 
```
class MyClass 
{ 
	// with in class field initializers
	public readonly int[] myArray = { 1, 2, 3 };

	public void ModifyArray()
	{ 
		myArray[0] = 10; // this is fine because we're modifying the contents of the array
	}
}

MyClass obj = new MyClass();

obj.ModifyArray();
obj.myArray = new int[] { 4, 5, 6 }; // this will result in an error
```
**READONLY IS NOT THE SAME AS CONST**. 


### Difference Between ReadOnly and Const
`const`: 
- Must be assigned a value at compile time
- the value cannot change at run time 
- Implicitly `static`, meaning it belongs to the type itself, not the instance, now this is very different than C++

`readonly`: 
- Can be assigned at run time, usually done in the constructor. 
- Is **NOT** implicitly `static`.

```
class ConstantExample
{ 
	public const int ConstValue = 100; // compile-time constant
	public readonly int ReadOnlyValue; // can be set at runtime 

	public ConstantsExample(int value)
	{ 
		ReadOnlyValue = value;
	}
}
```

##### `static readonly`
These can only be assigned once, either at the time of declaration or in a static constructor. 
This is great for initializing at run time and shared across all instances of a class. 
```
class MyClass 
{
	public static readonly string WelcomeMessage
	static MyClass () 
	{ 
		WelcomeMessage = "Welcome to C#";
	}
}
```



![[Pasted image 20240925235703.png]]

Quick note on static constructors: 
#staticconstructors
#### Static Constructors
They are special constructors that initializes static members of a class or struct. 
It is called **automatically** before any static members are accessed or any instance of the class is created. 
It cannot take any parameters, and it cannot be called directly. It is invoked automatically at run time. 
It's only run once in a thread safe manner. 
```
class MyClass 
{ 
	static MyClass() 
	{ 
		...
	}
}
```


```
class MyClass
{ 
	public static int Count; 


	static MyClass() 
	{ 
		Count = 0; // initialize the static field
		Console.WriteLine("Static CTOR Called");
	}

	public MyClass() 
	{ 
		Count++;
		Console.WriteLine("Normal CTOR Called");
	}
}

Console.Writeline("Before Creating instances!:");

// Static Constructor is Called here
MyClass obj1 = new MyClass(); // normal instance ctor called count = 1
MyClass obj2 = new MyClass(); // normal instance ctor called count = 2
```

Another good example: 
```
class DataBase
{ 
	public static string ConnectionString{ get; private set;}

	static Database()
	{ 
		ConnectionString = "Some long ass thing right here";
	}
}

var other = Database.ConnectionString;
```

# Ref Structs 
A special kind of struct that can only live on the stack, not on the heap, designed to be more efficient in terms of memory, remaining local, on the stack. 
They are not subject to garbage collection, sort of like C++. 
They cannot be stored in heap allocated fields. 
They cannot be converted into `object` or `dynamic`, or any interface type. 
Cannot be captured by lambdas. 

A good use is `Span<T>` and `ReadOnlySpan<T>`, they are most popular in the .NET framework, as they represent memory slices and highly efficient for working with contiguous memory regions, like arrays, or stack-allocated memory, without any unnecessary copying. 

```
ref struct MyRefStruct 
{ 
	public int X;
	public int Y;

	public MyRefStruct(int x, int y)
	{ 
		X = x;
		Y = y;
	}

	public void Print()
	{ 
		...
	}
}
```
The key restraints are that: 
- They cannot be used as a field in a class: which is on the heap as it is a reference type. 
- Not to be used in async methods. 
- They cannot be used in iterators. 

The `Span<T>` example: 
```
int[] array = { 1, 2, 3, 4, 5, 6 };
Span<int> span = array.AsSpan();

span[0] = 10; // modifying the SPAN
// this will also modify the original array
```
![[Pasted image 20240926002420.png]]

Remember that in C#, arrays are reference types. 
Meaning that when we create an array, the variable is essentially what we might consider a C++ pointer to somewhere on the heap, copying that gives us a shallow copy. 

