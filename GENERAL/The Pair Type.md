[[Associative Container]]
#pairtype

This is defined in the `<utility>` header. 
It obviously holds two data members. 

Like containers, it is a template which we generate specific types, template initialization. 
Supplying two type names when we create a pair. 
The data members of the pair has the corresponding types. 
There is no requirement that the two types be the same: 
```
pair<string, string> anon; // holding two strings
pair<string, size_t> word_count; // holds a string and an size_t
pair<string, vector<int>> line; // holds string and vector<int>
```

Default constructor will just construct an empty string or vector etc.. 
`size_t` will initialize to 0. 
We could also provide initializers in init list : 
`pair<string, string> author{"James", "Joyce"};`

These data members are public, and they are named `first, second` respectively. 
```
// print the results
cout << w.first << " occurs" << w.second << ((w.second > 1)? " times" : " time") << endl;
```
Here `w` is a reference to an element in a map (as they are pairs). 
Elements in a `map` are pairs. 
In this statement we are printing the `first` member and the `second` member, which is the counter. 
There are a limited number of operations that we can perform on pairs. 

![[Pasted image 20240329025845.png]]

### A Function To Create Pair Objects
Imagine, just imagine, just imagine, ok just imagine, that we have a function that needs to return a `pair`. 
Under the new standard we can list initialize the return value: 
```
pair<string, int> // return type
process(vector<int> &v)
{ 
	// process v
	if(!v.empty())
		return {v.back(), v.back().size()}; // list intialize
	else
		return pair<string, int>(); // explicitly constructed return value.
}
```
`v` not being empty, we return a `pair` composed of the last `string` in `v` and the size of that `string`. 

They are fine return types, even thought the first one doesn't look like we are declaring the type, however, it's the same as if we did something like this: 
```
bool returnTrue(){ 
	return true;
}
```
We can initialize the return, without declaring the type inline, as it is already declared earlier on. 

```
pair<string, int> f = {"Hugo", 25};
```
This is also fine too; 
```
auto f = pair<string, int>(); 
cout << f.first << " : " << f.second << endl;
```

We might also say: 
```
if(!v.empty())
	return pair<string, in>(v.back(), v.back().size());
```
Alternatively, we could have used `make_pair` to generate a new `pair` of the appropriate type from its two arguments: 
```
if(!v.empty())
	return make_pair(v.back(), v.back().size());
```

