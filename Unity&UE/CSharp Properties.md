A property is a member that provides a flexible mechanism to read, write or compute the value of a data field. 
They appear as public data members, however, they are implemented as special methods called *accessors*. 
A field is a variable of any type that is declared directly in a class or struct. 
A field defines a storage location: 

```
public cclass Person
{ 
	public string? FirstName; // remember that the ? creates a 
	// Nullable<string> meaning that FirstName can be null
	// The alternative being FirstName = string.Empty();
}
```

### Property Definitions
A property definition contains declarations for `get` and `set`. 
When we do something like this: 
```
public class Person 
{ 
	public string? FirstName { get; set; }
}
```

This is **auto implemented property**. 
The compiler will generate hidden backing field for the property. 
Then we can specify the value for the property. 
Rather than using null let's use `string.Empty`
```
public class Person
{ 
	public string FirstName { get; set; } = string.Empty;
}
```

The compiler here is generating code that will handle the retrieval and storage of the "FirstName property. 

##### Backing Field Generation: 
So a property is not a field, therefore, the compiler has to generate something to actually hold the values here, you won't see this in the code, however, you will see it in the compiled output. 

Then we the compiler will generate the accessors too. 

```
private string? _firstName = string.Empty;
public string? FirstName 
{
	get { return _firstName;}
	set { _firstName = value;}
}
```

This is what the compiler generates for us automatically here. 
As it will generate the backing field automatically for us here. 

[[Question Mark]]
Remember that `private string? _firstName;`
Means that `_firstName` can hold either a string or Null. 
The whole thing makes it more readable. 


Remember in python, that thing that was pretty confusing abouyt properties, where you had a data member that you wanted to access, however, perhaps there were some things you wnted to do when this field was accessed, so you would use a property. 
Which sort of blurs the lines between fields and methods that would get them. 
That's what this is, yet we have a succinct `get` and `set`. 
`get` and `set` here are known as accessors. 


Similar to python : 
```
class Person: 
	def __init__(self): 
		self._first_name = ""

	@property
	def first_name(self): 
		return self._first_name

	@first_name.setter
	def first_name(self, value): 
		if not value:
			raise ValueError("First name cannot be empty")
		self._first_name = value
# remember that in python the _ before a variable indicates that it is private
// Might look something like this in C# 

public class Person 
{ 
	private string? _firstName;

	public string? FirstName
	{ 
		get { return _firstName; }
	
		set 
		{ 
			if(string.IsNullOrEmpty(value))
				throw new ArgumentException("First Name Cannot Be Empty.");
			_firstName = value;
		}
	}
}
```

#### What does this look like in C++ 
```
class Person { 
private: 
	std::string firstName;
	
public: 
	std::string GetName() const 
	{ 
		return firstName;
	}

	void setName(const std::string& newName)
	{ 
		firstName = newName;
	}
};
```

Read only in C#: 

```
public class Person 
{ 
	private string? _firstName;
	
	public string? FirstName 
	{
		get;
	} 
}

public class Person
{ 
	public string Name { get; }
	pubic Person(string name)
	{ 
		Name = name;
	}
}


// in C++ read only would jsut a private setter, or not setter 
```


### Indexers
Remember that in C++ you would have to override the [] operator: 
```
class StringCollection { 
private: 
	std::vector<std::string> strings;
public: 
	// accessing the actual thing itself
	std::string& operator[](size_t index)
	{ 
		// might want to do some checking here
		return strings[index];
	}
};
```


### Overall 
Properties provide a controlled way to access and modify the values of private member variables. 
1. Member variable - the actual data storage within a class. 
2. Property - acting as an interface to the member variable. They combine the getter and setter methods into a single construct, so we can encapsulate access to the member variable. 

