#stringstreams
#theiolibrary [[The IO Library]] 
#fileinputandoutput[[File Input and Output]]

The `sstream` header defines three types to support in-memory IO; 
these types read from or write to a `string` as if the string were an `IO` stream. 

The `istringstream` types reads a `string`, `ostrinstream` writes a `string`, and `stringstream` reads and writes the `string`.

Remember that these types will inherit from `iostream` as well. 
![[Pasted image 20240313135914.png]]

### Using an `istringstream`
Often used when we have work to do on an entire line, and other work to tod with individual words within a line. 

Assume we have a file that lists people and their associated phone numbers. 
Some only have one number, but others have several. 
Our input file might look like: 
```
morgan 2015552368 862550123
drew 9735550130
lee 6095550132 3015550175 8005550000
```
Each starts with a name, which is followed by one or more phone number. 

```
// members are publicly by default
struct PersonInfo{ 
	string name;
	vector<string> phones;
};
```
Our program will read the data file and build up a `vector` of `PersonInfo`. 
Each element in the `vector` will correspond to one record in the file. 
Process the input loop that reads a record and then extracts the name and phone numbers for each person. 

```
string line, word; 
vector<PersonInfo> people; 
// read the input a line at a time until cin hits the EOF
while(getline(cin, line)){ 
	PersonInfo info;
	istringstream record(line); // bind record the line we just read
	record >> info.name; // read the name
	while(record >> word)
		info.phones.push_back(word); // and store them
	people.push_back(info);
}
```


Here we use `getline()` to read an entire record from standard input. 
If that succeeds then `line` holds the record from the input file. 
Next we bind an `istringstream` to the line that we just read. 
We can now use the input operator on the `istringstream` to read each element in the current record. 
The while loop will read data from the string (stream), rather than from standard input. 


### Using `ostringstream`s
They are useful when we need to build up our output a little at a time but do no want to print the output until later. 
Remember that `istringstream` is used for input operations, treating strings as a source of input. We can extract from that string using >>. 

Back to `ostringstream`. 
We might want to validate and reformat the phone numbers we read in the previous example. 
If all the members are valid, we want to print a new file containing the reformatted numbers. 

If any invalid number, we won't put them in the new file. 
Instead we'll write an error message, containing the person's name and a list of their invalid numbers. 
We wouldn't then be able to produce this output straight away, we have to validate, and then write that output with only the numbers that we can use. 
So we can "write" the output to an in-memory  `ostringstream`.

```
for(const auto& entry : people){  // for each entry in people, entry is a synonym for that person in people, however const means we won't access
	ostringstream formatted, badNums; // objects created on each loop
	for(const auto &nums: entry.phones){ // for each number in our phones vector
		if(!valid(nums)){ 
			badNums << "" << nums; // string into badNumbs sstream
		}else
			// writes to formatted strings
			formatted << "" << format(nums);
	}
	if(badNums.str().empty()) // there were no bad numbers
		os << entry.name << "" // print the name
			<< formatted.str() << endl;
	else // otherwise, print the name and bad numbers
		cer << "input: error" << entry.name << "invalid number(s)" << badNums.str() << endl;
}
``` 

Here it's clear that we have assumed two functions, `valid` and `format`, that will validate and reformat.

## My Examples: 

```
#include <sstream>

int main(){ 
	stringstream ss;
	string name = "Hugo"; 
	int age = 25;

	ss << "Name is " << name << " age is " << age;
	string element; 
	while(ss >> element){ // here it will split by " ", spaces
		cout << element << endl;
	}
}
```
![[Pasted image 20240313145543.png]]
 Say if we changed it to ` name << "age is" ...`.  You would get the output of 
 ```
 is
 Hugoage
 is
```
As there is no space to separate.

By default the delimiters are whitespace. 

To note that we can use the other functions that we would with `fstreams` and `iostreams` like `eof()`

```
string input = "42 3.14 Hello", element;
    stringstream ss(input);
    while(ss >> element){
        cout << element << " : ";
        if (ss.eof())
            cout << "Reached end of stringstream" << endl; 
        }
```
![[Pasted image 20240313151249.png]]


Remember this idea: 
that when we for range over using references, it's like doing this in python: 
```
nlist : list = [x for x in range(100)]
slist : list = []

for x in range(len(nlist)-1):
    nlist[x] = str(nlist[x]) + ":"
```

However in C++, thanks to references and (pointers): 
```
vector<int> vInt; 
for(int i = 0; i <= 100; ++i){ 
	vInt.push_back(i);
}

for(auto &i : vInt){
	++i;
}

for(auto i :vInt){ 
	cout << i << " ";
}
cout << endl;

vector<int>* vectpoint = &vInt;
cout << vectpoint << "   " << (*vectpoint)[0] << endl;
vector<int> &synonym = vInt;
cout << synonym[2] << endl;
```

![[Pasted image 20240313153346.png]]
Very cool indeed. 



Something to remember is this: if i had this: 
```
vector<int> vint;
    for(int i = 0; i<=100; ++i){ vint.push_back(i); }

    for(auto f : vint){ cout << f << "  ";}
    cout << endl;
```

Then f is an object in its own right, it copies the value of the variable in vint; 
However, if I did: 
```
for(auto& f : vint)
```

Then f just directly refers to an already created object and we don't need to copy anything. 
Just something to remember, if you wanna save on some memory. You would want to do this for large objects too right. 


Delimiting using `getline()`: 
```
std::string input = "abc,def,ghi";
std::istringstream ss(input);
std::string token;
while(std::getline(ss, token, ',')){
	std::cout << token << "\n";
}
std::cout << endl;
```
so rather than limit out, we limit in, then print out result of that limit in. 


