Functions with the same name, but different parameter lists and that appear in the same scope are **overloaded**. 
Such as: 
```
void print(const char *cp);
void print(const int *beg, const int *endl);
void print(const int ia[], size_t size);
```
They perform the same general action, but to different parameter types. 
The compiler will deduce automatically depending on the type of the argument that we used. 

```
int j[2] = {0,1};
print("Hello World"); // calls print(const char*)
print(j, end(j) - begin(j)); // calls print(const int*, size_t)
print(begin(j), end(j)); // calls print(const int*, const int*)
```

Not really thought about it,  but the whole `end(j) - beging(j)` just returns the size of the array: 
So in this case, it returns 2. 

The main function cannot be overloaded. 

### Defining Overloaded Functions
Imagine a function that is designed to find something in a database based on name, phone number, account number, and so on. 

Overloading lets us define a collection of functions, each named `lookup`, that differ in terms of how they do the search. 
```
Record lookup(const Account&); // find by Account
Record lookup(const Phone&); // find by Phone
Record lookup(const Name&); // find by Name

Account acct; 
Phone phone; 
Record r1 = lookup(acct); // call a version that takes an Account
Record r2 = lookup(phone); // call version that takes a Phone
```

They all share the same name, yet they are three distinct functions. 

In order to overload: 
- The number of parameters must be different
- The type of parameters must be different. 

It would be erroneous for functions to differ only in terms of their return types. If the parameter lists match, but the return types differ, then the second declaration would be the error, as the first is defined already before the second. 

```
Record lookup(const Account&);
bool lookup(const Account&); // ERROR
```

### Determining Whether Two Parameter  Types Differ
Two parameters lists can be identical, even if they don't look the same: 
```
// each pair declares the same function
Record lookup(const Account &acct);
Record lookup(const Account&); // Paramter names are ignored

typedef Phone Telno; 
Record lookup(const Phone&);
Record lookup(const Telno&); // They are the same type
```
In the first pair, the declaration names it parameter. They are only a documentation aid. They do not change the parameter list. 
It is important to note, that in the second of that pair, it requires a reference to an Account type, however, it doesn't give a name, so we wouldn't be able to actually use that function. This just gives the function the ability to overload, by passing it a type that we might not actually use in the function, however, we want to run that function when we use that type. However, they would work the same in this case, in that the first pair are of the same function. 

Remember that type alias does not produce a new type, but rather just uses synonyms. 

### Overloading and `const` Parameters
You should remember, that top level const has no effect on the objects that can be passed to the function. As we are making a copy of that int.
However, if we were to reference a const int, ie, then we would have to put the const in the parameter list. That is just the same as const outside of parameter declaration. 
A parameter that has top-leve const is indistinguishable from one without a top-level const: 

```
Record lookup(Phone);
record lookup(const Phone); // redeclares Record lookup(Phone)

Record lookup(Phone*);
Record lookup(Phone* const); // redeclares Record lookup(Phone*)
```

Both pairs, the second, declares the same as the first. 

We can overload based on whether the parameter is a reference (or pointer) to the const or non-const version of a given type; such consts are low-level;

```
// functions taking const and nonconst references or pointers have different parameters
// declarations for four independent, overloaded functions
Record lookup(Account&); // NEW function, Ref to Account
Record lookup(const Account&); // NEW function that takes a const reference

Record lookup(Account*); // NEW function, takes a pointer to an Account
Record lookup(const Account*); // NEW function, takes a pointer to const
```
The compiler will use the `const`ness in the argument to distinguish which function to call. 

There is no conversion, from const, we can pass a const object only to the version with a `const` parameter. 

As there is a conversion to a const, we can call either function on a non`const` object or a pointer to non`const`. 
The compiler will prefer the non`const` version when we pass a non-const object or pointer to non`const`. 

### `const_cast` and Overloading
Const_cast usage:
```
void modifyInteger(const int& x) {
    int& mutable_ref = const_cast<int&>(x); // Cast away constness
    mutable_ref = 42; // Now we can modify x through mutable_ref
}

int main() {
    int num = 10;
    modifyInteger(num); // Passes a const reference to num
    // Now num is modified to 42
    return 0;
}
```

Remember the return string, that returns the reference to the shorter string: 
```
const string& shorterString(const string &s1, const string &s2){ 
	return s1.size() <= s2.size() ? s1 : s2;
}
```
Try not to overload functions that do similar things. 

The function takes and returns references to `const string`. 
We can call the function on a pair of `nonconst string` arguments, but we'll get a reference to a const string as the result. 
We might want to have  the function so that when we give it `nonconst` arguments, it would yield a plain reference. 

We can write this version of the function: 
```
string &shorterString(string &s1, string &s2){ 
	auto &r = shorterString(const_cast<const string&>(s1), const_cast<const string&>(s2));
	return const_cast<string&>(r);
}
```

This version calls the const version of `shorterString`. The auto part will recognise that we have returned from the const version, a const reference to a string, which is bound to one of our original, `nonconst` arguments. Therefore, we know it is safe to cast that `string` back to a plain string& in the return.  

```
const string &shorterString(const string &s1, const string &s2){
    cout << "Called constant version" << endl;
    return s1.size() <= s2.size() ? s1 : s2;
}

string &shorterString(string &s1, string &s2){
    cout << "Called normal version" << endl;
    auto &r = shorterString(const_cast<const string&>(s1), const_cast<const string&>(s2));
    return const_cast<string&>(r);
}

  
int main(){
    string a("Hello world"), b("a");
    auto c = shorterString(a,b);
    cout << c << endl;
```

The output here would be: 
![[Pasted image 20240224180633.png]]

