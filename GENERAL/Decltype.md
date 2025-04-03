`decltype(auto)`

### Returning a Value
When the expression is an rvalue or a non-reference type; 
```
int x =5; 
decltype(auto) val = x; // val will be an int
decltype(x) val = 20; // val also an int
```
### Returning a Reference
```
int x = 5; 
int& ref = x;
decltype(auto) val = ref; // val will be an int& 

decltype((x)) val = x; // val will be an int&  here too
```

```
int& getRef(int& num)
{ 
	return num;
}

decltype(auto) val = getRef(x); // e is int& (reference)
```
### Returning an Rvalue Reference
```
int&& c = 20;
decltype(auto) val = std::move(c); // val is int&&
```
#### Why Does `auto` Suck Nuts
`auto` will always decay, therefore it is great for certain circumstances, however, if we need a reference, then we should be used `decltype`. 
That's why we need to add the value modifier on there as well. 

