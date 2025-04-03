#nonmemberclassrelatedfunctions
They are conceptually part of the interface, however, they are not part of the class itself. 

Defined as any member would be, perhaps we separate the declaration and the definition. 

These functions, are typically declared in the same header as the class itself. 

They are "part of the class" (kind of) just declare it in the same header ifle as the class. 


### Defining the `read` and the `print` Functions 
They do the same job as the functions from earlier. 
The bodies of our functions look a lot like the code here: 
```
// input transactions contain ISBN, number of copies sold, and sales price 
istream &read(istream &is, Sales_data &item){ 
	double price = 0;
	is >> item.bookNo >> itemm.units_sold >> price;
	item.revenue = price * item.units_sold;
	return is;
}

ostream &print(ostream *os, const Sales_data &item){ 
	os << item.isbn() << "" << item.units_sold << "" << item.revenue << "" << item.avg_price();
	return os;
}
```


[[istream]]
`istream` is a class in C++ that represents an input stream, a sequence of characters from a source such as the keyboard, a file, or another input device. 
Part of the input/output stream library, allowing for reading from and outputting to various data sources. 
The `istream` class provides member functions for reading data from the input stream, such as the operator `>>`, `getline()`, `get()`. 
It is considered the base class of classes like `istringstream`, `ifstream` and `cin`.

[[ostream]]

The `read` function reads data from the given stream into the given object. 
The `print` function prints the contents of the given object on the given stream. 
Both functions take a reference to their respective IO class types. 

The IO classes are types that cannot be copied, we can only pass them in reference. 

Moreover, reading or writing to a stream changes that stream, so both functions take ordinary references, not references to `const`, to the stream objects. 

Functions also should do minimal formatting, that way the user can decide whether the newline is needed. 

### Defining the Add Function
The `add` function takes two `Sales_data` objects and returns a new `Sales_data` object representing their sums: 
```
Sales_data add (const Sales_data &lhs, const Sales_data &rhs)
{
	Sales_data sum = lhs; // copy data members from lhs into sum
	sum.combine(rhs); // add data members from rhs into sum
	return sum;
}
```

In this function we are defining a new `Sales_data` object named `sum`, that will hold the sum of our two transactions. This is initialized as a copy of `lhs`. 
By default, copying a class object means that we copy that object's members to. 

After this, the `bookNo`, `units_sold` and `revenue` members of `sum` (our new object), will have the same values of those of `lhs`. 
Then we `combine` to add the `units_sold` and `revenue` members of `rhs` into `sum`. Then we return a copy of `sum`. 
