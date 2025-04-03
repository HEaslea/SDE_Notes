They are left associative. 

IO will use the overloaded << operators. 

LEFT ASSOCIATIVE
`cout << "Hi" << "There" << endl;`
`((cout << "Hi"() << "There" ) << endl;`

You have to make sure that the precedence is right when we write couts. 
```
cout << 42 + 10; 
cout << (10 < 42); 
cout << 10 < 42; This would result in an error, trying to ompare cout to 42
(cout << 10) < 42; That's the equivalent of the last one
```


## The `sizeof` Operator
This is right hand associative. 
Two forms: 
`sizeof(type)   and     sizeof expr`
In the second form, sizeof will return the size of the return type. 

``` 
Sales_data data, *p; 
sizeof(Sales_data); //  size required to hold an object of type Sales_data
sizeof data; // same things as above really
sizeof p; // sizeof a pointer
sizeof *p // size of the type to which p points, again Sales_data
sizeof data.revenue // size fo the type of Sales_data's revenue number
sizeof Sales_data::revenue // alternative ways to get to the same thing
```

We can find the size of an array, by finding the sizeof the total array, divided by the sizeof the objects in the array. 

```
// sizeof(ia)/ sizeof(*ia) returns the number of elements in ia
constexpr size_t sz = sizeof(ia) / sizeof(*ia); 
int arr2[sz]; // ok sizeof returns a constant expression
```

```
int arr[] = {1,2,3,4,5}; 
cout << sizeof(arr); // will output 20, as 4 * 5
cout << sizeof(arr)/sizeof(*arr); // 20 / 4, = 5
cout << sizeof(arr)/sizeof(decltype(arr[0])) << endl; // is exactly the same
```

Because arrays are a fixed size this means that 


## Comma Operator
Guarantees the order in which its operands are evaluated. 
An example here would be: 
```
vector<int>::size_type cnt = ivec.size(); 
// assigns values from size... 1 to the elements of ivec
for(vector<int>::size_type ix= 0; ix != ivec.size(); ++ix, --cnt){ 
	ivec[ix] = cnt;
}
```
It's like an `and` idea. That both `cnt` is decremented and `ix` is incremented.