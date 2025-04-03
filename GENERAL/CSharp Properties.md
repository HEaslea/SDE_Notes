```
public class Person 
{ 
	private string _name;
	
	public string Name
	{ 
		get { return _name }
		set { _name = value; }
	}
}
```

### Auto Implemented Properties
```
public class Person
{ 
	public string Name { get; set; }
	public int Age { get; set; }
}
```
Here the compiler will generate the hidden backing field. 

#### Properties with Backing Fields
```
public class Person 
{ 
	private string _name;
	public string Name
	{ 
		get 
		{ 
			return _name;
		}	
		set 
		{ 
			if(!string.IsNullOrEmpty(value))
			{ 
				_name = value;
			}
		}
	}
}
```

##### Read Only and Write Only Properties
Read Only
```
public class Person 
{ 
	public string Name { get; } // read only 
	public pPerson(string name)
	{ 
		Name = name; // this feels very odd
	}
}
```
Write Only: 
```
public class SecureData
{ 
	private string_password;

	public string Password
	{ 
		set { _password = value; } // the write only section
	}
}
```


#### Init Only Properties
```
public class Person 
{ 
	public string Name { get; init; }
	public int Age { get; init; }
}

// usage 
var person = new Person 
{ 
	Name = "Alice";
	Age = 25;
};

// person.Name = "Bob"; // Error init only property cannot be set
```

#### Access Modifiers for Getters and Setters
```
public class Person 
{ 
	public string Name { get; private set; } // public getter, private setter

	public int Age { get; protected set; } // only in this class and child classes. 
	
}
```

Here: 
- Other extern code can read Name, however, only in-class can set, for Name.
- For Age, can read from anywhere, however, only child or in class can set it. 

The reason for these is that we don't really want to write everything so broadly, getters and setters should be really simple, they're not so simple in C++ like this, but you can write them to be here: 
```
public class Person
{ 
	private int _age;

	public int Age
	{ 
		get => _age;

		set 
		{ 
			if(value < 0 || value > 150)
				throw new ArgumentOutOfRangeException(nameof(value), "Reason");
			_age = value;
		}
	}
}
```

