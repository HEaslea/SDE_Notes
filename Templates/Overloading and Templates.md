Function templates can be overloaded by other templates or by ordinary, non-template functions. 

Functions with the same name must differ either in types of params or number of params. 

Function matching will be affect in the following ways due to templates: 
- The candidate functions for a call include any function-template instantiation for which template argument deduction succeeds. 
- The candidate function templates are always viable, as the template argument deduction will have eliminated any templates that are not.
- The viable functions (template or not) are ranked by conversions that are required, if any. The conversions that are used to call a function template are more limited. 
- If one function provides a better match than any of the others, that function is chosen. If there are several functions providing equally good matches then : 

If there is only one non-template function in the set of equally good matches, the non-template function is called (reducing instantiations)
The compiler will prefer the nontemplate types. 
If there are no non-template types, yet there are multiple function templates, and one of these templates is more specialized than any of the others, the more specialized function template is called. 

If not, and otherwise, then the call is ambiguous. 

### Writing Overloaded Templates
Let's build a set of functions that might be useful during debugging. 

They'll be called `debug_rep`, each of which will return a `string` representation of a given object. 
Starting with the most general version of this function as a template that takes a reference to a `const` object: 
```
// print any type we don't otherwise handle
template <typename T> string debug_rep(const T &t)
{ 
	ostringstream ret; 
	ret << t; // uses T's output operator to print a representation of t
	return ret.str(); // return a copy of the string to which ret is bound
}
```
Kind of boring :(

Any object that has an output operator. 

Now the version for pointers: 
```
// print pointers as their pointer value, followed by the object to which the pointer points
// NB; this function will not work properly with char*
template <typename T> string debug_rep(T *p)
{ 
	ostringstream ret;
	ret << "Pointer: " << p; // print the pointer's own value
	if(p)
		ret << "" << debug_rep(*p); // pasing the object itself
	else
		ret << "Null Pointer"; // indicate p is null
	return ret.str(); 
	// return a copy of the string to which ret is bound
}
```
This wont' work for `char*` as the IO defines the version of the `<<` for`char*`, will assume the pointer denotes a null-terminated character array, and prints the contents of the array, not its address. 
We might see later how we should address this. 

```
const char* str = "Hello there";
cout << str << endl; // output is Hello there
```

We might use them as followed: 
```
string s("hi");
cout << debug_rep(s) << endl;
```

Only the first `debug_rep` here is viable. The second requires a pointer, this call has a non pointer object. 

There is then only one viable function calling. 
Let's call it with a pointer: 
Just need to pass the address: 
`cout << debug_rep(&s) << endl;`
The issue now is that we have two viable instantiations: 
- `debug_rep(const string* &)`, the first one bound with `T` to `string*`. 
- `debug_rep(string)`, the second function, with `T` bound to `string`. 
the instantiation of the second version of `debug_rep` is an exact match for this call. The first will require a conversion of the plain pointer to a `const`  pointer. 

#### Multiple Viable Templates
Consider this call now: 
```
const string* sp = &s;
cout << debug_rep(sp) << endl;
```

Both templates are just as viable here: 

Normal function matching will not be able to distinguish between the two calls. 
There is a special rule for overloaded functions, the call will resolve `debug_rep(T*)`, which is the more specialized template. 
> **When there are several overloaded templates that provide an equally good match for a call, the most specialized version will be preferred**

### Nontemplate and Template Overloads
Defining nontemplate/ ordinary version of `debug_rep` to print `string`s inside double quotes: 
```
// print strings inside double quotes
string debug_rep(const string& s)
{ 
	return "" + s + "";
	// boss, i'm just going to use std::quoted
}
```
Calling on a string: 
Quickly note: 
```
string f("Hello World");
const string& rf = f;
f = "Hugo";
rf = "Morton"; // ERROR
```

The `debug_rep` can be passed an rvalue: Without the `const` in the parameters, we wouldn't be able to pass in an rvalue. 

```
string s("Hi");
cout << debug_rep(s) << endl;
```

There are two equally good viable functions: 
- `debug_rep<string>(const string&)` the first template bound to `string`
- `debug_rep(const string&)`, the nontemplate function. 
They basically have the same parameter list, providing equally good matches. 
The **nontemplate** version will be chosen. 
Equally good matches, where there are template and nontemplate, the nontemplate will be chosen. 
There is a reduction in template instantiations. 

> **The basic here is that when there are equally good picks, the nontemplate function will be chosen**. 


### Overloaded Templates and Conversions
Pointers to `C-style` character strings and string literals. 
So we have a version that takes a `string`. 
Consider this: 
`cout << debug_rep("Hi World!") << endl;`
This will call `debug_rep(T*)`. 

Here all three (`:-(`) are viable: 
- `debug_rep(const T&)` with `T` bound to `char[10]`.
- `debug_rep(T*)`, with `T` bound to `const char`. 
- `debug_rep(const string&)`, requiring a conversion from `const char*` to `string`. 

Array to pointer, that conversion is considered as an exact match for function-matching purposes. 

As we had mentioned earlier, the version that is more specialized, that one will be selected. 

We might also do something like this: 
```
// convert the character pointers to string and call the string version of debug_rep

string debug_rep(char *p)
{ 
	return debug_rep(string(p));
}

string debug_rep(const char* p)
{ 
	return debug_rep(string(p));
}
```

#### Missing Declarations Can Cause the Program to Misbehave

If the right version of a function is not in scope, then we might call the wrong version: 
`template <typename T> string debug_rep(const T& t);`
```
template <typename T> string debug_rep(T* p); 

// the following declaration must be in scope 
// for the definition of debug_rep(char*) to do the right thing
string debug_rep(const string&);
string deubg_rep(char *p){
	// if the declaration for the version that takes a const string& is not in scope
	// the return will call debug_rep(const T&) with T instantiated with string
	return debug_rep(string(p));
}
```


Remember that usually, if we don't define a function or just don't link it, then the code just won't compile. 
However, that's not the case for the templates. 
If the compiler can instantiate the call from the templates that are provided, then the missing declaration won't matter. 
There will be a sort of silent instantiation. 

> **Declare every function in an overload set before you define any of the functions. That way you don't have to worry whether the compiler will instantiate a casll before it sees the function you intended to call**

