The default constructor is used automatically whenever an object is default or value initialized. 

Default initialization will happen: 
- when we define non`static` variables or arrays at block scope without initializers
- when a class that itself has members of class type uses the synthesized default constructors
- when members of a class type are not explicitly initialized in a constructor initializer list: 

Example of each one by one: 
```
// defining an array without initializers
int arr[5]; // can lead to issues if we want to access elements
```

Value Initialization: 
- During array initialization when we provide fewer initializers than the size of the array. 
- When we define a local static object without an initializer. 
- When we explicitly request value initialization by writing an expressions of the form `T()` where T is the name of a type (the vector constructor that takes a single argument to specify the vector's size uses an argument of this kind to value initialize its element initializer).

```
void funct(){ 
	static int x; // will intialize to 0, not undefined
	cout << x << endl;
}
```

Classes must have a default constructor in order to be used in these contexts. 

Take a look at a class that has data members, but do not have a default constructor: 
```
class NoDefault{
public: 
	NoDefault(const std::string&);
	// additional members follow, but no other constructors
};

struct A{ 
	// my_mem is public by default
	NoDefault my_mem;
};

A a; // cannot synthesize a constructor for A
struct B{ 
	B() {} // error no initializer for b_member
	NoDefault b_member;
};
```
In practice it is almost always right to provide a default constructor if other constructors are going to be defined. 

### Using Default Constructors
Defining an object that uses the default constructor of initialization is to leave off the trailing, empty parentheses; 
```
// ok: obj is a default-initialized object
Sales_data obj;
```

#### Implicit Class-Type Conversions
Classes can define implicit conversions as well. 

Every constructor that can be called with a single argument defines an implicit conversion to a class type. 
We'll see later the idea of converting a class type to another type. 

A constructor that can be called with a single argument defines an implicit conversion from the constructor's parameter to the class type. 

That is, we can use a `string` or an `istream` where an object of type `Sales_data`
```
string null_book = "9-999-99999-9";
// consructs a temporary Sales_data object 
// with units_sold and revenue equal to 0 and bookNo equal to null_book
item.combine(null_book);
```
We've called the `combine` function with a string argument. 
Now this is perfectly legal apparently; the compiler automatically creates  a `Sales_data` object from the given string. 
It's like normal type conversion, however, the compiler here is actually pretty smart and converts the "little type" (in your head at least) to the "larger" class type. 
That new `Sales_data` object is then passed to `combine`. 
Because `combine`'s parameter is a reference to `const`, we can pass a temporary to that parameter.

##### Only One Class-Type Conversion is Allowed
The compiler will ONLY APPLY ONE CLASS-TYPE CONVERSION. 
The following code is in error: 
```
// error : requires two user-defined conversions: 
// (1) convert "9-999-99999-9" to string
// (2) convert that (temporary) string to Sales_data
item.combine("9-999-99999-9");
```
If we wanted to make this call, we can do so explicitly converting the character string to either a `string` or a `Sales_data` object.

```
// ok: explicit conversion to string, implicit conversion to Sales_data
item.combine(string("9-999-99999-9"));
// ok: implicit conversion to string, explicit conversion to Sales_data
item.combine(Sales_data("9-999-99999-9"));
```

#### Class-Type Conversions Are not Always Useful
How will our users use these conversion. 
[Great Video on Implicit Conversion](https://www.youtube.com/watch?v=Rr1NX1lH3oE)
```
class Entity{ 
private: 
	std::string m_name;
	int m_Age;
public: 
	Entity(const std::string& name) : m_Name(name), m_Age(-1) {}

	Entity(int age) : m_Name("Unknown"), m_Age(age) {}
};

void PrintEntity(const Entity& entity){ 
	// Printing
}

int main(){ 
	Entity a = "Cherno";
	Entity b = 22;
}
```

Now these bottom two work because there are constructors that work by having parameters of the type that they are inputting. 

So there is a string constructor, therefore we can do the whole 
`Entity a = "Cherno";` Otherwise this wouldn't work. 

**Implicit Construction**  is a thing now. 
You can use that however the fuck you want fella. 

The idea is that C++ thinks that it can convert the literals into an `Entity` in this case. 

However if we were to do something like this: 
`PrintEntity("Cherno");` that WOULDN'T work, as it's not a string in this case, it's an array of char. And that wouldn't work in terms of converting to an entity. This would be two conversions, one from array to string, then string to entity. 
We could do `PrintEntity(std::string("Cherno"));`

Or we could do: `PrintEntity(Entity("Cherno"));` As it would be implicitly converting the "Cherno" into a string and then into an Entity. 

Cherno doesn't really like it all that much; 
`Entity b(22);` is a much nicer version; 

An `explicit` means that we cannot implicitly call the constructor. 
If we changed the int constructor to look more like this: 
`explicit Entity(int age) : m_Name("Unknown") , m_Age(age) {}`

We would no longer be able to say `Entity b = 22;` or `PrintEntity(22);` As we cannot use the constructor in order to convert the int into an entity. 

Remember that the variables are initialize in the order that they are declared.

If the string one was explicit as well : `PrintEntity(Entity("Cherno"));` would still be fine, as we are still calling the `Entity` constructor Explicitly and creating a sort of Temporary Entity to pass into the print. 

Remember that the converting constructors are just with the single arguments. 

Once that function finishes we have no access to that temporary object anymore. 

Printing a temp object is a little weird, not really useful, but you might need to use it later. 

### Suppressing Implicit Conversion Defined by Constructors
Using explicit like we looked at a little earlier: 
```
class Sales_data{ 
public: 
	Sales_data() = default;
	Sales_data(const std::string &s, unsigned n, double p) : 
		bookNo(s), units_sold(n), revenue(p*n) {}
	explicit Sales_data(const std::string &s) : bookNo(s) {}
	explicit Sales_data(std::istream&);
	// remaining members as before
};
```

Now, neither members that could be used, can be used. 
So if we did: 
```
item.combine(null_book); // error: string constructor is now explicit
item.combine(cin); // error; istream constructor is explicit
```
Of course this only really matters for constructors that only have one argument. 
And it would only be used on the declaration inside the class. 
Not repeated on the definition outside the class. 
```
// error: explicit allowed only on a constructor declaration in a class header
explicit Sales_data::Sales_data(istream& is)
{ 
	read(is, *this);
}
```

#### `explicit` Constructors Can be Used only for Direct Initialization
One context in which implicit conversions happen is when we use the copy form of initialization (remember copy and direct initialization) (with an =). 
We cannot use an explicit constructor with this form of initialization; we must use direction initialization ( remember that classes only really are new types ). 
```
Sales_data item1 (null_book); // ok: direct initialization
// error now: cannot use the copy form of initialization with an explicity 
Sales_data item2 = null_book;
```

When a constructor is declared `explicit`, it can be used only with the direct from of initialization, this is pretty obvious, but something to remember. Honestly too, you should just be doing this on the regular `Entity a(20);` etc. 


#### Explicitly Using Constructor for Conversions
Although the compiler will not use an `explicit` constructor for an implicit conversion, we can of course use these constructors explicitly to force a conversion: 
```
// ok: the argument is an explicitly constructed Sales_data object
item.combime(Sales_data(null_book)); // nice
// ok: static case can use an explicit constructor
item.combine(static_cast<Sales_data>(cin));
```

In the first one, we create a temporary object in order to use it for `item.combine`, using the constructor that uses the type of `null_book`. 

The second uses a static_cast to perform the explicit conversion, it uses the `istream` constructor here to create the temporary object. 

#### Library Classes with `explicit` Constructors
Some of the library classes that we have used have single-parameter constructors: 
- The string constructor that takes a single parameter of type `const char*` is not explicit. 
- The `vector` constructors that takes a size is `explicit`. 
So basically, some libraries will have constructors that require explicit creations. 
Therefore the vector that creates a container of a certain size is explicit. Really, honestly, this is only important in the scheme that you are aware of this case, and you could create this case in your own libraries. 

