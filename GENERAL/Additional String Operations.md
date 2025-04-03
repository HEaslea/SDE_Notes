`string` does give us a number of additional operations beyond those common to the sequential containers. 
They are useful for the close interaction between C-style character arrays, or they add version that let us use indices in place of iterators. 
They are defined in the string library. 

Best to skim, as there are a lot. 

### Constructing Strings
#constructingstrings
![[Pasted image 20240318013224.png]]

```
const char *cp = "Hello World!!!"; // null terminated array

char noNull[] = {'H', 'i'}; // not null terminated

string s1(cp); // copy up to null in cp, as string doesn't have a null terminator
// therefore s1 == "Hello World!!!"

string s2(noNull, 2); // copying two chars from no_null s2 == "Hi"

string s3(noNull); // undefined; noNull not null terminated
// works on mine, but not assured.

string s4(cp + 6, 5); // copy 5 chars starting at cp[6] 24 == "World"

string s5(s1, 6, 5); // copy 5 chars starting at s1[6]; s5 == "World"

string s6(s1, 6); // copy from s1[6] to end of s1; s6 == "World!!!";

string s7(s1, 6, 20); // ok, copies only to end of s1; s7 == "World!!!"

string s8(s1, 16); // throws an out_of_range exception, as no null termination
```
starting position, and, count.

Normally when we create a `string` from a `const char*`, the array to which the pointer points must be null terminated; chars are copied up to null. 

If we pass a count and there is no null, or if the given count is greater than the size of the array, the operation is undefined. 

 ### The `substr` Operation
 Returns a string that is a copy or part/all of the original. 
 We can pass `substr` an optional starting position and count 
```
string s("Hello World");

string s2 = s.substr(0,5); // s2 = hello

string s3 = s.substr(6); // s3 = world, no count given, assumed to give to the end of word

string s4 = s.substr(6, 11); // s3 = world

string s5 = s.substr(12); // throws an out_of_range exception
```

![[Pasted image 20240318114844.png]]If the `pos` exceeds the size of the string, then we get an exception. 

```
int main(){ 
	string s("Hello World");
	try { 
		string s1 = s.substr(100, 3);
	} catch (std::out_of_range& e) { 
		cout << "!!!ERROR!!!: " << e.what();
	}
	cout << endl;
}
```


### Other ways of changing a String 
The sequential container that is `string` will support a host of other operations: 
`assign, insert, and erase` operations as well as additional versions of `insert and erase`.
These `insert and erase` versions will also support an index argument. 
```
s.insert(s.size(), 5, '!'); // insert five exclamation points at the end of s
s.erase(s.size()-5, 5); // erase the last 5 elements in s
```

we also have: 
```
const char *cp = "Stately, plump Buck";
s.assign(cp, 7); // s == "Stately"
s.insert(s.size(), cp + 7); // insert at end , s == "Stately, plump Buck"
```

Remember that insert will insert before the pointer we have, therefore saying `s.size()` is fine here. 

We can also ask that the `insert and assign` chars come from another `string` or substring thereof.

```
string s = "some string", s2 = "some other string";
s.insert(0,s2); insert copy of s2 before position 0 in s
// insert s2.size() characters from s2 starting at s2[0] before [0]
s.insert(0, s2, 0, s2.size());
```
Again, like most others, the compiler will tell you an amazing amount of information about all of these members. 
So many overloads, therefore we need some help to know what each one is. 

### `append` and `replace` Function
#appendandreplacefunction
append is a short hand way of appending at the end. 
```
string s("C++ Primer"), s2 = s; // initialize s and 2 to "C++ Primer";
s.insert(s.size(), " 4th Ed."); // s == "C++ Primer 4th Ed."
s2.append(" 4th Ed."); // equivalent: appends " 4th Ed." to s2, now s == s2;
```
![[Pasted image 20240318125746.png]]

The `replace` operations are shorthand way of calling `erase` and `insert`. 
```
s.erase(11, 3); // s == "C++ Primer Ed."
s.insert(11, "5th"); // s == "C++ Primer 5th Ed."
// starting at position 11, erase three characters and then insert "5th"
s2.replace(11, 3, "5th"); // equivalent : s == s2
```

We can insert a larger or smaller string with `replace`;
`s.replace(11,3,"Fifth"); // s == "C++ Primer Fifth Ed."`

### The Many Overloaded Ways to Change a `string`
#overloadedstringchanges
There are lots of overloaded functions for changing strings. They all share a common interface, making it much easier to interchange between them. 

The new chars can be from a `string` or from a `char*` , from brace-enclosed list of chars, or as a char and a count. 
When they are from a `string` or a `char*` we can adjust how much of those are to be copied. 


### `string` Search Operations
There are size different search functions, each of which have four overloaded functions. 
Each of these returns a `string::size_type, which is an index of where the match occurred. 
If there is no match, it returns a `static` member named `string::npos` . 
This is described as a `const string::size_type` init'd with the value of -1.
As `npos` and is an `unsigned` type, this init means that `npos` is == to the largest possible size any `string` could have. 
![[Pasted image 20240318132944.png]]

There is this idea as well : 
```
string name("AnnaBelle");
auto pos1 = name.find("Anna"); // pos1 == 0
```

Remember here that case matters:
```
string lowercase("annabelle");
pos1 = lowercase.find("Anna"); // pos1 == npos
```

Remember that if we go `<0` with an unsigned int, then it will rollover to the largest version it can take. 

Then there is `find_first_of` and `find_first_not_of`.

Where we will have something that looks like: 
```
string numbers("0123456789"), name("r2d2");
// will return 1, ie. the index of the first digit in name
auto pos1 = name.find_first_of(numbers);
```

`find_first_not_of` will find us the index of the first char that is not of the search term that we have defined.. 

#### Specifying Where to Start the Search
Passing optional starting position to `find` operations. 
By default, the position is set to 0.

One common programming pattern uses this optional argument to loop through a `string` finding all occurrences: 
```
string::size_type pos = 0;
// each iteration finds the next number in name
while((pos = name.find_first_of(numbers, pos)) != string::npos){ 
	cout << "Found number at index: " << pos << " element is" << name[pos] << endl;
	++pos; // move to the next character
}
```
The increment might seem a little odd. 
However, if we didn't do it, then the loop would never terminate. 
If we didn't terminate, then on the second trip through the loop, the `pos` in the `find_first_of` will still be 0, therefore, it would keep returning the same index over and over. 
Therefore we need to increment, due to the fact that the function will be called first. 

### Searching Backwards
the `rfind` will allow us to do this
```
string river("Mississippi");
auto first_pos = river.find("is"); // returns 1
auto last_pos = river.rfind("is"); // returns 4
```

`find` returns an index of 1, indicating the start of the first `"is"`, while `rfind` returns an index of 4. 


## The `compare` function
#comparefunctions
These are similar to the C library `strcmp`. 
`s.compare` returns 0 or positive or negative values depending on whether `s` is == to, greater than, or less than the string formed from the given arguments. 

![[Pasted image 20240318151756.png]]We might, again, compare the whole string or a portion thereof. 

### Numeric Conversions
Like in C, chars can be in int. Like we would with `int c; c = getchar();`. 
The new standard have introduced a number of functions that convert between numeric data and library `string`s: 
```
int i = 42; 
string s = to_string(i); // converts the int i to its characte representation
double d = stod(s); // converts the string s to floating point
```
![[Pasted image 20240318152256.png]]

```
string s2 = "pi = 3.14";
// convert the first substring in s that starts with a digit, d = 3.14
auto d = stod(s2.substr(s2.find_first_of("+-.0123456789)));
```
If they can't be converted to a number, these will thrown a `invalid_argument` exception. 
Of it they finalize to a value that can't be represented, they throw an `out_of_range` exception as well. 

