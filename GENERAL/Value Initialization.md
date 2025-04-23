**Does it use the default constructor?**: 

#### Class Types
Say that we have our own `MyClass` : 
```
strcut MyClass 
{ 
	MyClass() { ... }
};

MyClass obj{}; // DOES USE THE DEFAULT CONSTRUCTOR
```

Or the compiler can synthesize one - remember that the compiler will generate certain constructors or `=` assignment operators. 

#### For Built In Types
`int x{}; // x == 0`  This will value initialize them to 0. 

#### For POD Structs (Plain-old-data)
```
struct POD
{ 
	int x; 
	double y;
};

POD p{}; // p.x = 0, p.y = 0.0
```


#### Value Initialization
```
T obj{};
T obj = T{};
```
Both will call **default constructor** for class types.
Zero-initializes **built-in types**. 
Zero-initializes **POD** structs. 

```
int x{}; // x == 0
std::string s{}; //  s == "" - calls the default constructor
```


#### Default Initialization
```
T obj; // No Braces or Equals
```
**Class Types** - calls the default constructors (if there is one).
**Built-in Types** - the value is initialized (could contain absolute garbage - be careful with ints). 

#### Direct Initialization
`T obj(args)`
Just passing to the constructor of `T`. 
No implicit conversions - picks the best-matching constructor. 

```
std::string s(5, 'a'); // s == "aaaaa"
MyClass obj(42); // calling constructor with int parameter
```

#### Copy Initialization
`T obj = args;`
This is equivalent to `T obj = T(args);`
There can be some conversions going on here, unlike the others. 

```
std::string s = "hello"; // implicit conversion from const char* to string
MyClass obj = 42; // If MyClass(int) exists, this works through implicit conversions
```

