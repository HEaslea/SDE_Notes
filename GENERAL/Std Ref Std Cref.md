`std::ref` constructs an object of the appropriate `reference_wrapper` type to hold a reference to an object. 
`auto r = ref(x);`
`r` is a `reference_wrapper` here. `r` is not a direct reference to `x`. 

`reference_wrapper` is great when we want to emulate a reference to a thing, with the ability to copy it. 

The idea is that you cannot copy references in the same way that you can for other variables. 
As they do not have their own storage. 

```
int a = 10;
int & refA = a;

int& refB = refA; // this is fine and refB is an alias for a
// Note there is no copying here

// However, doing something like this we run into an error
int& refC = (refA);
```

So then using `reference_wrapper` may look something like this: 
```
void printValue(int& value)
{ 
	cout << value << endl;
}

int main() 
{ 
	int a = 10;
	int b = 20;

	std::vector<std::reference_wrapper<int>> refs = { std::ref(a), std::ref(b)};
}
```

However, if you did something like this: 
```
std::vector<int&> refs = {a, b};
```

This is because `std::vector`, need their objects to be copyable and moveable. 
References are not objects, they do not have their own storage. In fact when we put references in function parameters: we are really just passing arguments. 

With references, anything done on the original, any expression acted on it, is acted on the original. 
```
int foo = 1; 
int &bar = foo;
int a;
a = bar; // actually we're copying the foo, bar is JUST AN ALIAS
```
#### Avoiding Slicing
`Base& d = derivedObject();`
This is something that we come across quite often, usually done with pointers. 
If were were to have a container that holds base class objects, that should be derived objects, then we are slicing the objects, which isn't great. 

However, if we were to use `std::reference_wrapper` then we can maintain polymorphism. 


#### Function Parameter Passing
```
void increment(std::reference_wrapper<int> ref)
{ 
	reg.get()++; // increment the referenced value
}
```
