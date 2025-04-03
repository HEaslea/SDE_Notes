[CPPREF StringStream ](https://www.youtube.com/watch?v=-7hJAD7mjz4&t=2073s) 
[CPPREF Basic_StringStream](https://en.cppreference.com/w/cpp/io/basic_stringstream)

If we did something like: 
```
string inputs = "Hello there, how are you";

stringstream ss(inputs);
string token;

while(ss >> token){ 
	cout << token;
}
cout << endl;
```


The idea is that we initialize `ss` using `inputs`, we need token as a temp string in order to get from `ss` to `cout`. 

`string tokne; stringstream ss(inputs); while(ss >> token){// do something with that word}`

```
using ss = stringstream;
string inputs = "Hello there, how are you", token;
ss os(input);
while(os >> token){ 
	for(auto& c : token){ 
		c = toupper(c);
	}
	cout << token << endl;
}
// output 
HELLO
THERE,
HOW
ARE
YOU
```
 boost also has a library that will help with some of the more simple algorithms: 
```
#include <boost/algorithm/string.hpp>
#include <string>

string str = "Hello World";
boost::to_upper(str);
```

Although we don't have boost at this moment upon writing. 

