The `IO` library makes extensive use of `>>/<<`. 
IO lib defines versions of these operators to read and write built-in types. 

Classes that have their own input and output usually define these on their own too. 

#### Overloading Output Operator `<<`

The first param is usually and output operator is a ref to a non`const` ostream object. 
non`const` as we are writing to the stream, changing its state. 
Also has to be a reference, because we can't copy an `ostream` object. 

The second parameter ordinarily should be a ref to `const`, of the class type want to print. 
The param here is ref to avoid copying, can be `const` too, printing an object does not change that object. 

To be consisten with other output operators, `operator<<` will return its `ostream` parameter. 


```
ostream& operator<<(ostream &os, const Sales_data &item)
{ 
	os << item.isbn() << "" << items.units_sold << "" << item.revenue << "" << item.avg_price();
	return os;
}
```
This is basically identical to a print function. 
Then we just return a ref to the `ostream` it just wrote. 
`cout << Sales_data_item;`, `cout` is sent into the `operator` overload as `cout` as a reference, remember that we can't copy it. 

##### Minimal Formatting
Output operators for built-in types usually do little if any formatting. 
They do not print newlines. 
So we would want things to behave similarly. 
So just don't format them all that much. 

> Output Operators should print the contents of the object, with minimal formatting. They should not print a newline. 

##### IO Ops Must be Nonmember Functions
Remember that if they were, the LHO would have to be an object of our class, so that we can bind `this` to it. 

```
Sales_data data;
data << cout; // this would have to be what it's like if we have << as a member function
```

Here, `data` would have to be a of type `istream/ostream`. 
We wouldn't be able to add `operator<<` to the stl though of course. 

These functions then must be `friends` with the class that it will be printing from. 

##### Overloading Operator `>>`
So the first operand is usually a reference to the stream from which it is read, and the second is the object into which to read. 

The operator usually returns the reference to its given stream, the second param must be non`const` because the purpose of the input operator is to read data into this object. 

```
istream& operator>>(istream& is, Sales_data &item)
{ 
	double price; // not need to init here
	// we'll read into price before we use it
	is >> item.BookNo >> item.units_sold >> price; 
	if(is) // check that the inputs succeeded
		item.revenue = item.units_sold * price;
	else 
		item = Sales_data(); // input failed; give the object hte default state
	return is;
}
```
So here, we want to see clearly that when we write something like `istream is; if(is)` we are implicitly converting the `is` of type `istream` into a `void*`, or `nullptr` if the stream is in a failed state. 
Therefore, we can write `if(is)`. 

So unlike the output, we have to deal with the idea that the input will fail, if it does, we have to reset the object to a default state. 
Therefore we have a consistent state, if we reach an error. 

> Inputs have to deal with the idea that the input might fail; output operators generally don't bother. 

#### Errors During Input
Some errors that might happen are: 
- A read operation might fail because the stream contains data of an incorrect type. eg. after reading `bookNo`, the input operator assumes that the next two items will be numeric data. If this doesn't work, then the bad flag activates, then the rest of the stream will fail. 
- Any of the reads could hit `EOF` or some other error on the input stream. 

Rather than checking after every read, we just check once after reading all the data and before using the data points: 
```
if(is)
	item.revenu = item.units_sold*price;
else 
	item = Sales_data(); // input failed, create object 
	// with default state
```
If there is a failure, we just say, "Don't worry about which read has failed" we'll just make sure the whole object is default. 
This just means that any errors mid way through, we aren't returning a "mid-way-through" finished object, we return a complete, but maybe a default object. 

By doing this, we are making sure that the user has a default object, at least it works, so that they don't have to worry about using the object in a certain state, or about input errors. 

> Input operators should decide what, if anything, to do about error recovery. 

##### Indicating Errors
Some input operators need to do additional data verification. 
We might check if the `bookNo` is inappropriate. If it is, we might set the input to indicate failure, even though the IO part of it is successful. 

Usually we set on the `failbit`. 
`badbit` would indicate that the stream was corrupted. 
`eofbit` is for exhausted streams. 
These last two are more for the `IO` library to deal with, rather than us explicitly. 

