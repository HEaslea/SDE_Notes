IN C#, you may have something that looks like `int? nullableInt = null;`
For nullable value types (`T?`): 
The symbol makes the type Nullable. 
Useful, when a value might not be set of is optional. 

The `int?` is short Hand for `Nullable<int>`. Allowing an `int` to hold the `null` type, along, obviously with any integer value. 

## Null-Coalescing Operator 
`??`, used to provide default value in the case a nullable type is `null`. 
A common way to handle potential null values: 
```
int? nulableInt = null;
int value = nullableInt ?? 0; // if nullableInt is null then value is 0
```

## Null-Conditional Operator
Here we will only access that thing if the variable is NOT NULL. 

```
string? name = null;
int? length = name?.Length; // length will be null if name is null
```

## Ternary Conditional Operator
```
int x = 5;
string result = x > 0 ? "Positive" : "Negative";
```

## Pattern Matching 
```
object obj = 5;
if(obj is int? number) // This checks if obj is a nullable int
{ 
}
```


```
int? y = null;
int? x = y; // x will null if y is null
```
```
string? y = null;
int? length = y?.Length; // Length will be null as y is null
```
```
int? y = 5;
int? x = y; // x will be 5S

int? y = null;
int x = y ?? 10; // x will be 10 as y is null
```



