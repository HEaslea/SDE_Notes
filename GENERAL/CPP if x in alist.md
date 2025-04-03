#set #setiterators
[[Associative Container]] [[Sequential Containers]]

This is the C++ equivalent of python's beautiful `if x in alist: # do something`. 
We just use iterators, and algs in order to do this in the first case: 
```
set<string> fruit = {"apple", "banana", "orange", "pineapple"};
string tocheck = "banana"; 

if(fruit.find(tocheck) != fruit.end()){ 
	cout << tocheck << " is in fruit set";
} else { 
	cout << tocheck << " is not in fruit set";
}
cout << endl;
```

### Getting the Index of the Found Object
#findingtheindex
```
set<string> fruit = {"apple", "banana", "orange", "pineapple"};
string tocheck = "orange";

auto result = fruit.find(tocheck);
if(result != fruit.end())
{ 
	cout << "Found at: " << std::distance(fruit.begin(), result);
}
```
The reason we use distance, is because a lot of iterators will not have the benefit of the `-` operator. It's only defined on random access iterators, which makes a load of sense now, as mathematical operations will be fine on those. 