There are no defined statements to do input or output. 

The std library, which is extensive, will provide some input and output. 

`#include <iostream>`

There are two types here : 
1. istream - input stream
2. ostream - output stream

Stream is a sequence of chars read from or written to an IO device. 

Standard input and output objects: 
There are four IO objects: 
1. cin (see in) - standard input - console input - type istream
2. cout (see out) - standard output - console output - type ostream
3. cerr (see err ) - standard error object - console error - type ostream
4. clog (see log) general information - type ostream 

Mr Robert Martin has a good visual for it here: 
[Mr Robert Martin video about cin.get, cin.putback etc. ](https://www.youtube.com/watch?v=yMwA4u7eXEY)

```
char ch; 

std::cout << "Enter a string: ";

// get one char at a time until new line char \n is encountered
while(std::cin.get(ch) && ch != '\n){ 
	cout << "Char read: " << ch << endl; } 
```

