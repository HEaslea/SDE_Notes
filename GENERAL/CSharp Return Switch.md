The idea being: 
Syntax: 
```
return value switch 
{ 
	case1 => result1, 
	case2 => result2, 
	dfeault => defaultResult
};
```


A major example being: 
Here is the "normal" "boring" way of doing it: 
```
public string GetDayName(DayOfWeek day)
{ 
	switch (day)
	{ 
		case DayOfWeek.Monday:
			return "Monday";
	
		case DayOfWeek.Tuesday:
			return "Tuesday";
			
		default: 
			throw new ArgumentOutOfRangeException(nameof(day), "Invalid Day");
	}
}
```

Now if we spiced it up again:  This is what we really want
```
public string GetDayName(DayOfWeek day)
{ 
	return day switch 
	{ 
		DayOfWeek.Monday => "Monday", 
		DayOfWeek.Tuesday => "Tuesday", 
		DayOfWeek.Wednesday => "Wednesday", 
		... 
		_ => throw new ArgumentOutOfRangeException(nameof(day), "Invalid Day");
	};
}
```

Notice a couple of things, that we only really have ;  at the end of the switch, and at the end of the default in the return switch. However in the first, it has to be after every return. 

Note as well that the switch is just  the same as per usual, instead of `switch (expr)` we do `return (expr) switch`

We can even go a step further and work with a tuple (something that we can deconstruct in C#). 

```
static string DescribeWeather(int temp, bool isSunny)
{
    // using a tuple here
    // these work the same way
    return (temp, isSunny) switch
    {
        ( > 30, true) => "It's a hot and sunny day",
        ( > 30, false) => "It's hot and cloudy today",

        _ => "Unsure"
    };
}
```

Here we are constructing a tuple from the args that we get, 

Remember that we don't have any fall through in C#, remember, this is a coddling language. 

You can even do something like this: 
```
int number = 2; 
string result = number switch
{ 
	1 => "one", 
	2 => "two", 
	3 => "three", 
	_ => "unknown"
};
```
If we wanted fall through: 
```
switch (value)
{ 
	case 1: 
	case 2: 
		C.WL("Case 1 or 2");
		break;
	case 3: 
		C.WL("Case 3");
		break;
	default: 
		C.WL("Default");
		break;
}
```

Don't forget the break at the end here. 

