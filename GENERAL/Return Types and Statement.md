`return` terminates the function that is currently executing and returns control to the point from which the function was called. 
There are two forms of `return` statements: 
` return and return *expression*`. 

#### Functions with No Return Value
Within a `void` function, you would find no return statement, it is automatically added once the final statement of the function has occurred. 
You may find them in the actual function however: 
```
void swap(int &vi, int &v2){ 
	if(v1 == v2){ return; }
	int tmp = v2;
	v2 = v1; 
	v1 = tmp;
	// compiler will "place" a return here
}
```

### Returning a Value#
Every other return type must return a value. 

Or it returns a type that can be converted to the specified return type in the definition. 


```
incorrect return values, this will not compile 
bool str_subrange(const string &str1, const string &str2){ 
	// same sizes; return normal equality test
	if (str1.size() == str2.size())
		return str1 == str2; // fine, as returns bool
		
	// finding size of the smaller string with the conditional operator
	auto size = (str1.size() < str2.size()) ? str1.size() : str2.size();
	
	// look at each element up to the size of the smaller string
	for(decltype(size)i = 0; i != size; ++i){ 
		if (str1[i] != str2[i]){ 
			return; // error as no return value
		}
	}
	// error here too, as control might flow past the end of the function without a return
	// compiler might not detect this error
}
```

##### How are values returned
The return value is used to initialize a temporary at the call site, and that temp is the result of the function call. 

```
// return the plural versin of word if ctr is greater than 1
string make_plural(size_t ctr, const string &word, const string &ending){ 
	return (ctr > 1)? word+ending : word;
}
```
the return type here is string, meaning that the return value is coped to the call site. 
This function returns a copy of word, or it returns an unnamed temp string that results from adding word and ending. 

Consider: 
```
// return a reference to the shorter of two strings
const string &shorterString(const string &s1, const string &s2){ 
	return s1.size() <= s2.size() ? s1 : s2;
}
```
The params and return types are references to const string. 
The strings are not copied when the function is called or when the result is returned. 

##### Never Return a Ref or a Pointer to a local Object

#### Functions that Return Class Types and Call Operator
The call operator has the same precedence as the dot and arrow operators. 
It is obviously left associative. 
If a function returns a pointer, a ref or an object of class type, we can use the result of a call to call a member of the resulting object. 

```
// call the size member of the string returned by shorterString
auto sz = shorterString(s1, s2).size();
```

Remember than a lvalue is an object, that has defined storage, meaning we can take its address, and rvalue cannot do this, it's just a value. 

An lvalue can have an rvalue. 

##### Reference Returns are Lvalues
Such as: 
```
char &get_val(string &str, string::size_t ix){ 
	return str[ix]; // assuming ix is valid
}
```
```
int main()
{ 
	string s("a value"); 
	cout << s << endl; // prints a Value; 
	get_val(s, 0) = 'A'; // changes s[0] to A
	cout << s << endl; // prints A Value;
}
```

Functions that return references tend to be on the left hand side. What we're returning here is a reference to `str[0]`. 
So essentially we get `str[0] = 'A';`

We wouldn't be able to say: 
`shorterString("hi", "bye") = "X"` as the return type is a const. 

### List initializing from the Return Value
Recall the idea of the `error_msg`. 

```
vector<string> Process()
{ 
	// ... 
	// expected and actual are strings
	if(expected.empty()){ 
		return {}; // return empty vector 
	}
	else if(expected == actual){ 
		return {"FunctionX", "okay"}
	}
	else{ 
		return {"Function X", expected, actual}
	}
}
```

### Return From Main
Main is allowed to return without a value. 
Compiler implicitly inserts a return of 0. 
Weirdly, the return of 0 from the main indicates a success; most others will indicate failure. 
Nonzero has a machine-dependent value. 
`cstdlib` header defines two preprocessor variables that we can use to indicate success or failure; 
```
int main()
{ 
	if (some_failure)
		return EXIT_FAILURE;
	else 
		return EXIT_SUCCESS;
}
```

[[GENERAL/Recursion]]

# Tests and Notes Here
```
string &shorterString(string &s1, string &s2){
    return s1.size() <= s2.size() ? s1 : s2;
}

int main(){
    string s1("a"), s2("Longer String");
   shorterString(s1, s2) = "Was the shorter string";
    cout << s1 << "  :  " << s2 << endl;
}
```

Output : `Was the shorter string : Longer String`



The thing about this piece of code that was confusing at first: 
```
const string &shorterString(const string &s1, const string &s2){
    return s1.size() <= s2.size() ? s1 : s2;
}

int main(){
    string a("a"), b("Longer string");
    cout << shorterString(a,b);
}
```

s1 and s2 are initialized as const references to strings, therefore when they are returned as they are, they are already references, therefore, we do not have to convert them to references before hand.


```
const string &shorterString(const string s1, const string s2){
    return s1.size() <= s2.size() ? s1 : s2;
}

int main(){
    string a("a"), b("Longer string");
    cout << shorterString(a,b);
```
This does work BUT BUT HOWEVER HOWEVER this would fail in most instances, as it returns a reference to a function local variable, as both s1 and s2 are passed by value and are copied into the function. 
This is something that the book would argue never to do. 


![[Pasted image 20240223033531.png]]

