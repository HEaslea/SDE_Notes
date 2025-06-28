The issue here is that when we have class `const` function that argues a `ptr`: 
eg.
```
class Test
{ 
	int* ptr{};

public: 

	Test(int* p) : ptr(p) {}


	void testPtr(int v) const 
	{ 
		*ptr = v; // this is fine
	}
};
```

Remembering that there is top level and bottom level `const`. 
**Top Level Const**: - applies to the variable itself

**Bottom Level Const**: - applies to the thing we are referring to : `int const* p;` `p` is a pointer to a `const` int. 


#### Bottom Level `const` **Propagation Rules**
```
int x = 5; 
const int* p = &x; // can point to a non-const as a const
```

```
// Here's an issue
const int x = 5; 
int* p = &x;
```
This is an issue as we are giving write-access to a `const` value through that reference. 

Remembering as well, that `auto` will drop that top-level `const`, however, will reserve that bottom level `const`. 

```
const int x = 42; 
const int* px = &x; // fine 

auto a = x; // int (top level const dropped)
auto b = px; // const int* (bottom level const preserved)
```

**Remember**: **Dropping of top level const**. 

#### With `decltype`
```
const int y = 7; 
decltype(y) a = 7; // notice decltype where <type> should be
// a is a const int
```


### Within Classes
Given the example earlier: 
```
class Example
{ 
	int* ptr; 
public: 
	Example(int* p) : ptr(p) {}

	void foo(int v) const 
	{ 
		*ptr = v; // this should not be ok given the idealogy of the const function
	}
};
```

