##### `auto` 
We know that `auto`, decays that which we are deducing from:
Hence why the forms: `auto&` and `auto&&` exists. 

Meaning:
References and `const` are dropped from top level. 

How then do we preserve reference or const:
`auto&` to preserve references. 
`const auto&` to preserve both `const` and reference. 

### `decltype(auto)`
This should become a shorthand way of thinking: > "Deduce the exact type (including `const`, references, etc) of the expression I'm assigning" <

```
int x = 42; 
int& ref = x; 
const int cx = x; 

auto a = ref; // int (ref dropped)
auto b = cx; // int (const dropped)

decltype(auto) c = ref; // int& (ref preserved)
decltype(auto) d = cx; // int& (ref const)
```

Very useful, when we write functions that forward or return expression and you want to preserve exact type. 

```
int& getRef(int& n) { return n; }

auto bad() 
{ 
	int a = 10; 
	return getRef(a); // BAD : the ref will get dropped in the auto deduction
}

decltype(auto) good() 
{ 
	int a = 10; 
	return getRef(a); // returns int& (reference preserved)
}
```


## `const T&` 
`const lvalue reference` 
This can bind to "almost" everything. 

Lvalue, Rvalues, `const`and non-`const`. 
Mostly due to the fact that we are using `const` - we know that we are not going to change anything about it, therefore, we can take in anything. 

Rvalues being passed in will have its lifetime extended until the ref is no longer needed. 


## Universal References
`T&&` for Forward Referencing. 
These are only called universal references when deduced in template, and is more of a Meyer's term. 

```
template<typename T> 
void f(T&& x); // T is a universal reference here
```

Due to reference cancelling : where only `&& &&` cancels into `&&`, all the others cancel into `&`. 

If we were only `void g(std::string&& s);` not universal, this can only bind to rvalues. 



