See [[istream]]

It is a class, representing the outputstream, a sequence of characters sent to the destination such as the console, a file, or another output device. 


`put()` puts a single char to the stream, `write()` writes a block of data to the stream, `flush()` all data in the stream outputted to the destination. 

```
std::ostream& output = std::cout;

    output << "Hello, world!" << std::endl;

    output.put('A');

    output.flush();
```


Knowing the destination: 
Most of this is done by the `ofstream` and `ostringstream`.

```
#include <iostream> 
#include <fstream>

int main(){ 
	std::ofstream outputFile("output.txt");
	if(outputFile.is_open()){ 
		outputFile << "This will be written to the file";
		outputFile.close()
	}
	else{ 
		std::cerr << "Failed to open the file for writing.";
	}
}
```

`cout` is an object/instance of `ostream` representing the standard output stream, usually the console. Typically for printing out to the console as you know:


In a sense, the object that we use, determines the destination. 

As you would expect: python also has a standard output too, 