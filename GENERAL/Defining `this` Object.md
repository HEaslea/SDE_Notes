#definingthisobject

[[Defining Abstract Data Types]]

The `combine` function is intended to act like the compound assignment operator `+=`. 
The idea being that the LHS operand is the object that called the function. 

```
Sales_data& Sales_data::combine(const Sales_data &rhs){ 
	units_sold += rhs.units_sold; // add the members of rhs into
	revenue += rhs.revenue; // the members of `this` object
	return *this; //returnning the boject on which the function was called;
}
```

In order to return an lvalue, which is what we want here as we are mimicking an operator. 
This `combine` function therefore requires a return of a reference, an lvalue (an object itself). 
Here, we need to use this, to access the object as a whole. Hence the whole `return *this;`
returning a pointer to the object that called the member function.


`this` works exactly like in python, we just use the member variables, assigned to the object. 
```
private: 
	int value; 
	string name; 
	double price; 

public: 
	MyClass(int val, string n, double p) : value(val), name(n), price(p) {}
	// note that the brackets stay in the brackets

	void PrintOut(){ 
		cout << "Name: " << name << endl;
        cout << "Value: " << value << endl;
        cout << "Price: " << price << endl;
	}
```

