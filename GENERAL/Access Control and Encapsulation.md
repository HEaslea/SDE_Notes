#accesscontrolandencapsulation
#classes [[Classes Start]]
#definingabstractdatatypes [[Defining Abstract Data Types]]

Interface describes what a structure does, while an implementation describes how the data structure does it. 

### Access Specifiers
- Members defined after a `public` specifier are accessible to all parts of the program. The `public` members define the interface of the class. 
- Members defined after a `private` specifier are accessible to the member functions of the class, not accessible to code that uses  the class. These `private` sections  
So again we can write: 
```
class Sales_data{ 
public:  // access Specifier added
	Sales_data() = default; // Constructor is compiler default
	Sales_data(const std::string &s, unsigned n, double p): 
		bookNo(s), units_sold(n), revenue(n*p){}
	Sales_data(const std::string &s) : bookNo(s) {}
	Sales_data(std::istream&); // constructor reading from input stream
	
	std::string isbn() const { return bookNo;}
	Sales_data &combine (const Sales_data&);
	
private:  // access specifier added
	double avg_price() const { return units_sold ? revenue/units_sold : 0;}
	std::string bookNo; 
	unsigned units_sold = 0;
	double revenue = 0;
}
```

Interface: Using the public specifier, member functions (`isbn` and `combine`). 
Implementation: using the `private` specifier; the data members and the functions that are part of the implementation

Just remember that interface is how we access the actual class. 

A class may not have access specifiers, so there are no restrictions on how often an access specifier may appear. 

### Using the `class` or `struct` keyword
You can define a class type using either `struct or class` it's really stylistic, that's it. 

The only difference here is the default access level: 
A class may define members before the first access specifier. 
If we use a struct, then the members defined before the first access specifier will be `public`: if we use `class`, then the members are `private`.
Just remember that `class`es are inherently private (you can't just walk into a classroom). 
`struct` s are inherently `public`. 

If we are intending for all our members to be public, then we would rather use a `struct`. 

If we do intend to have `private` members, then we would use a `class`.

That's the only difference, is the default access level. 

### Friends
#friends
Now that the data members are private; our `read`, `print`, `add` functions will no longer compile. 
Although they are part of `Sales_data` interface, they are not members of the class. 
They are not members of the class. 
Therefore what we can do is use the `friend` keyword. 
This will allow `nonmember` functions to access members. 

You would use a friend definition: 
```
class Sales_data{
	// friend declarations for non member Sales_data operations added 
	friend Sales_data add(const Sales_data&, const Sales_data&);
	friend data::istream &read(std::isteam, Sales_data&);
	friend std::ostream &print(std::ostream&, const Sales_data&);
	// other members and access specifiers as before


	public: 
		Sales_data = default(); 
		etc. 
		
	private:
		etc.
};

// declarations for nonmember parts fo the Sales_data interface
Sales_data add(const Sales_data&, const Sales_data);
std::istream &read(std::istream&, Sales_data&);
std::ostream &print(std::ostreawm&, const Sales_data&);
```

`friend` declarations may only appear inside a class definition; although they may appear anywhere in the class. 

Friends are not member of the class and are not affected by the access control of the section in which they are declared. 
We'll talk more about it later. 

### Benefits of Encapsulation 
#benefitsofencapsulation
Encapsulation provides two benefits: 
- user code cannot inadvertently corrupt the state of an encapsulated object. 
- The implementation of an encapsulated class can change over time without requiring changes in user level code. 
Keeping code to the implementation area of the class, means that most of the bugs that we suffer over objects, are going to be there, rather than scattered throughout each use of an object. 

THIS IS CRUCIAL: 
### Declarations of Friends
A friend declaration only specifies access. It is not a general declaration of a function. 
If we want uses of the class to be able to call a friend function, then we must also declare the function separately from the friend declaration. 
We usually declare each friend outside the class in the same header as the class itself. 

Some compilers my allow a call to a friend function when there is no ordinary declaration for that function. 
It's always a good idea to declare that function however, just in case. 
Shouldn't have to add and change a lot of code in every file if we change compiler. 

