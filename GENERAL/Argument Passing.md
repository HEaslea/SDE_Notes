If the parameter is a reference, then the parameter is bound to its argument. Otherwise, the argument's value is COPIED. 

When using a reference we say "**passing by reference**" or that the function is "**called by reference**". 

When it is copied, the parameter and the argument are independent objects. "**passed by value**" or "**called by value**". 

### Passing by Value
##### Pointer Parameters
Pointers will behave like any other nonreference type. 
When we copy a pointer, the value of the pointer is copied. After the copy, the two pointers are distinct. Yet, a pointer also gives us indirect access to the object to which that pointer points. 
We can change the value of that object by assigning through the pointer. 

```
int n = 0, i = 42; 
int *p = &n, *q = &i; 
*p = 42; 
p = q; // p now points to i; values in i and n are unchanged
```
The same behavior applies to pointer parameters: 
```
// function that takes a pointer and sets the pointer-to value to zero 
void reset(int *ip)
{ 
	*ip = 0; // changing value to which ip points
	ip = 0; // changes only the local copy of ip; the argument is unchanged.
}
```

The basic idea is that the pointer is copied, so there will be two distinct pointers, however, they will point to the same object. 

We could also write `reset(&i)` . 
```
2
0x61ff0c    0x61ff0c // the address value that is copied to the parameter
0x61fef0    0x61ff08 // the addresses of pointers, see they're distinct
0 // the function changes the value of i
```

```
void reset(int *ip){
    cout << ip << "\n" << &ip << "    ";
    *ip = 0;
    ip = 0;
}

int main(){
    int i = 2;
    cout << i << endl;
    int *ipp = &i;
    cout << ipp << "    ";
    reset(ipp);
    cout << &ipp;
    cout << i << E;
```
C programmers will generally use this over, the reference idea, however, C++ will use the reference idea, more fun that way :). 

### Passing by Reference

Recall that operations on reference are actually operations on the object to which the reference refers. 

```
int n = 0, i = 42; 
int &r = n; 
r = 42; 
r = i; n has a the same value as i
i = r; 
```
```
void reset(int &i)
```

We are passing an object directly, there is no need to pass its address in this case. 
Just as synonym, so it's like it's reaching into the main function. 

Doing this avoids creating copies. 
Some class types cannot be copied (including the IO types). 
```
// comparing the length of two strings
bool isShorter(const string &s1, const string &s2){ 
	return s1.size() < s2.size();
}
```
Reference parameters that are not changed inside a function should be references to const. 

##### Using Reference Parameters to Return Additional Information
A function may only return a single value. 
Yet a function might have more than one value to return. 
Reference parameters allow us to return multiple results. 
Define a function named `find_char`, that will return the position of the first occurrence of a given character in a `string`. 
And a function that will count how many times that character occurs. 
We could define a new type that contains the position and the count, but an easier solution is to pass and additional reference argument to hold the occurrence count: 
```
// returns the index of the first occurrence of c in s
// the ref parameter occurs counts how often c occurs
string::size_type find_char(const string& s, char c, string::size_type &occurs){ 
	auto ret = s.size(); 
	occurs = 0; 
	for(decltype(ret)i = 0; i != s.size(); ++i){ 
		if(s[i] ==  c){ 
			ret = i; // remember the first occurrence of c
			++occurs;
		}
	}
}


auto index = find_char(s, 'o', ctr);
```

### `const` Parameters and Arguments
Parameters that are `const`, it's important to remember the idea of top-level `const`. 
Top-level applies to the object (location) itself, the lvalue (rather than the rvalue). 

Remember that top level is fairly normal when it is not a pointer. It's still top level, when we have a pointer, we can have bottom and top leve. 


```
const int ci = 42; cannot change ci; const is top-level
int i = ci; top level const is ignored here
int *const p = &i; const top level again, cannot assign to p
*p = 0; this is fine, assignments through p are allowed. 
```

Just like here, top level consts are ignored when we copy an argument to initialize a parameter. We can pass either a top level const, or non-const through a parameter. 

`void fcn(const int i) { } fcn can read but not write to i`

We can call `fcn` with either a `const int` or a plain `int`. 
Top level is ignored ; this does lead toa very surprising implication: 
```
void fcn(const int i){ } can read but not write to i
void fcn(int i) { } redefines fcn(int)
```

In C++ we can define several different functions that have the same name. We can do so only if their parameter lists are sufficiently different. 
Top level consts are indeed ignored, we can pass either type into `fcn`. 
```
void fcn(int i){
    i = 30;
    LOG(i);
}

int main(){
    const int ci = 20;
    fcn(ci);
```

The int i and const int i, don't differ enough to mean that that idea works here. 

Also, for clarity: 
```
void fcn(const int i){ 
	int i = 30;
}
```
This obviously wouldn't work. 

### Pointer or Ref Parameters and `const`
Parameters are initialized in the same way that variables are initialized, it can be helpful to remember the general init rules. 
We can init an object with a low level const from a nonconst object, but not vice versa, if a low level const, then the init of it must be low level const too. 

```
int i = 42; 
const int *cp = &i; cp cannot change i; 
const int &r = i; r can't change i either
const int &r2 = 42; fine
int *p = cp; ERROR as types of p and cp don't match, p would have to be low level const as well
int &r3 = r; ERROR types of r3 and r don't match either
int &r4 = 42; ERROR can't init a palin ref from a literal
```
 The same rules apply to parameters too. 
```
int i = 0; 
const int ci = i; 
string::size_type ctr = 0; 
reset(&i); calls reset with the int* paramter
reset(&ci); ERROR can't init an int* from a pointer to a const int object
resert(i); calls with an int& parameter 
reset(ci); ERROR can't bind a plain reference to a const object ci
reset(42); ERROR can't bind a plain reference to a literal
reset(ctr); ERROR types don't match; ctr has an unsigned type
find_char("Hello World!", 'o', ctr); fine
```

We cannot pass: 
- a literal
- an expression that evaluates to an int
- an object that requires conversion
- or a const int object. 


### Use Reference to `const` when Possible
It is a common mistake to define parameters that a function doesn't change as a plain reference. 
Doing so gives the function's caller the misleading impression that the function might change its arguments values. 
We can't pass a const object, a literal, or an object that requires conversion to a plain reference parameter. 

Consider the `find_char` function that we had earlier. 
The function made its `string` parameter a reference to a `const with (const string &s)`. 
Had we defined it as: 
```
string::size_type find_char(string &s, char c, string::size_type &occurs)
```
we could call `find_char` only on a string object. 
A call such as `find_char("Hello World", 'o', ctr);` would fail at compile time. 

As well, if we were to use `find_char` in another function, then we would struggle eg: 
```
bool is_sentence(const string &s){ 
	// if there is a single period at the end, then s is a sentennce
	string::size_type ctr = 0;
	return find_char(s,'.',ctr) == s.size()-1 && ctr == 1;
}
```

If `find_char` took a plain string&, then this would be a compile time error. The problem is that s is a ref to a `const string`, but `find_char` was defined to take a plain string ref. 

The way to fix that would be in the `find_char` parameters. 

### Array Parameters
1. We cannot copy an array
2. When we use an array, it is usually converted to a pointer. 
Due to this conversion, when we pass an array to a function, we are actually passing a pointer to the array's first element. 

However, we can write a function that does look like it takes an array. 
```
// despite appearances, these three decs of print are equivalent
// each function has a single paramter of type const int*
void print(const int*)
void print(const int[]) // shows intent that the function takes array
void print(const int[10]) // dimension for documentation purposes (at best)
```
These will all be equivalent. 
When we we call print, the compiler will check to see if the argument is of type int*. 
Therefore we can pass in: 
``` 
int i = 0, j[2] = {0,1}; 
print(&i); fine &i is a int*
print(j); fine as j is converted to an int* that poitns to j[0]
```
Functs must also make sure that the pointers are in bounds of the array. 

As we only pass a pointer, we wouldn't know the size of the array, they must rely on additional information provided by the caller. 
These are three common techniques for this. 

#### Using a marker to Specify the Extent of an Array
C-style character strings has a null character, so it is very much like this. 

The function would know when to stop processing when they see this character. 
```
void print(const char* cp){ 
	if(cp) // cp is not a null pointer =
		while(*cp) // so long as the char it points to is not a null character
			cout << *cp++; // print the char
}


int main(){ 
	const char s[] = {"Hello World"};
	print(s); // This works fine
}
```
This works really well for data where there is an obvious end-marker (like the null character `\0`). Wouldn't work as well for a range of ints, where every value is legitimate. 

#### Using the Standard Library Conventions
Another is to pass the pointers to the first and one past the last element in the array. 
This is inspired by the techniques used in the standard library. 

```
void print(const int *beg, const int *end){ 
	while(beg != end){ 
		cout << *beg++ << endl;
	}
}
```

```
void print(const int *beg, const int *end){
    while (beg != end){
        cout << *beg++ << "  ";
    }
    cout << endl;
}

int main(){
    int i[] = {1,2,3,4,5};
    // we could pass two ways
    print(begin(i), end(i));
    auto beg = begin(i), last = end(i);
    print(beg, last);
```

Both of these are fine, as long as the typings match, and there aren't too many conversions.
#### Passing a Size Parameter
This is common in C programs. 
```
// const int ia[] is equiv to saying int* ia
// size is passed explicitly and used to control access to elements of ia

void print(const int ia[], size_t size){ 
	for(size_t i = 0; i != size; ++i){ 
		cout << ia[i] << "  ";
	}
	cout << endl;
}
```

Pretty simple. 
In order to get that size, there are a couple ways of doing it. 
```
void print(const int ia[], size_t size){
    for(size_t i = 0; i != size; ++i){
        cout << ia[i] << "  ";
    }
    cout << E;
}

int main(){
    int ia[] = {1,2,3,4,5,6,7};
    size_t t = sizeof(ia)/sizeof(ia[0]);
    print(ia, t);
```

This being the first. 
```
print(ia, end(ia) - begin(ia));
```

### Array Ref Parameters
Surely we can do this with arrays as well. 

```
void print(int (&arr)[10]){ 
	for (auto elem : arr){ 
		cout << elem << endl;
	}
}
```
Remember that `&arr[10]` declare as an array of 10 references.
`int (&arr)[10]` reference to an array of ten ints.

As a size of an array is part of its type, it is safe to rely on the dimension in the body of the function. However, the fact that the size is part of the type limits the usefulness of this version of `print`. 
```
int i = 0, j[2] = {0,1};
int k[10] = {0,1,2,3,4,5,6,7,8,9};
print(&i); // error as arg is not an array of 10 ints
print(j); // error for same reason
print(k); // fine as array of 10 ints
```

#### Passing Multidimensional Arrays
Remember that they are really just arrays of arrays. 
As we pass, we pass a pointer to the first array, which is the first element in the first array, the outer most dimension. 
You might have to pass the size of the row here too, such as: 
```
// matrix points to the first element in an array whoe elements are arrays of ten ints
void print(int (*matrix)[10], int rowsize){ };
```
The parenthesis around (matrix) are necessary. 
`int *matrix[10];` would be an array of ten pointers
`int (*matrix)[10];` pointer to an array of ten ints. 

The compiler ignores the first dimension: 
```
// equivalent definition
void print(int matrix[][10], int rowSize){/*...*/}
```
Declares matrix to be what looks like a two-dimensional array. In fact, the parameter is a pointer to an array of ten `ints`, not multidimensional. 

### `main` : Handling Command-Line Options 
So far we have used an empty parameter list for main. 
Sometimes we need to pass arguments into main. 

Imagine our main being an exe called `prog`, we might pass into it `prog -d -o ofile data0`. 

These are passed into main with two optional parameters
`int main(int argc, char *argv[]{ ... }`
The second one meaning that we are passing through an array of pointers to chars. 
An array of pointers to C-style character strings. The first, `argc`, passes the number of strings in that array. 

As the second is an array, we might define main as : 
`int main(int argc, char **argv){...}`
Indicating that `argv` points to a char*. 

When they are passed to main, the first element in `argv` points to either the name of the program or to the empty string. 

It would look something like this: with the element past last guaranteed to be 0: 
```
argv[0] = "prog"; 
argv[1] = "-d";
argv[2] = "-o";
argv[3] = "ofile";
argv[4] = "data0";
argv[5] = 0; element passed last. 
```

Remember that the first element is the name of the program; `prog`. 
The idea is here that we pass into main a few things from the command line. 


