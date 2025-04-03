#memberfunctionsoutsideclass
[[Defining Abstract Data Types]]

Of course the member's definition must match the declaration: return type, parameter list, and name must match, const too. 

```
double Sales_data::avg_price() const{ 
	if (units_sold)
		return revenue/units_sold;
	else
		return 0;
}
```
The scope operator, meaning that we are defining the `avg_price` that is declared in the scope of the `Sales_data` class. 

