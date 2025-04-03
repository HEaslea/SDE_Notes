`fstream` header defines three types to support file IO: 
`ifstream` to read, `ofstream` to print to and `fstream`, which reads and writes a given file. 

These types provide the same operations as those we have previously used on the objects `cin and cout`. Using << and >> to read and write files, we can use `getline` to read an `ifstream`. 
They inherit from the `iostream` types, Types in `fstream` add members to manage the file associated with the stream. 
These are `fstream` specific operations: 
![[Pasted image 20240312152912.png]]

Reading or writing to a file, we define a file stream, object and associate that object with the file. 
Each file stream class defines a member function named `open` that does whatever system-specific operations are required to located the given file and open it for reading or writing as appropriate. 
When we create a file stream, we can (optionally) provide a file name, so part of the constructing. 
```
ifstream(ifile); // construct an ifstream and open the given file
ofstream out; // output file stream that is not associate with any file
```

### Using an `fstream` in place of an `iostream&`
As noted before, we can use an object of inherited type in places where an object of the original type is expected. 
This means that functions written to take a reference to one of the `iostream` types can be called on behalf of the corresponding `fstream` ( or `sstream`) type. 

We can use the read and print functions to read and write to named files. 
```
ifstream input(arg[1]); // open the file of sales transactions
ofstream output(argv[2]); // open the output file
Sales_data total; // variable to hold the running sum
if(read(input, total)) { 
	Sales_data transl;
	while (read(input, trans)){ 
		if (total.isbn() == trans.isbn())
			total.combine(trans);
		else { 
			print(output, total) << endl;
			total = trans;
		}
	}
	print(output, total) << endl;
} else 
	cerr << "No Data?!" << endl;
```

### The `open` and `close` Members
When we define an empty file stream object, we can subsequently associate that object with a file by calling `open`: 
```
ifstream in(ifile);  // construct an ifstream, open the file
ofstream out; // output file stream that is not associated with any file 
out.open(ifile + ".copy"); // open the specified file
```

If a call to open fails, `failbit` is set. 
A call to open may fail, it is usually good to verify that the `open` succeeded. 

```
if(out) // check that the open succeeded
```
If it fails, then we will not attempt to use `out`. 

Once a file has been opened, it remains associated with the specified file. 
Calling `open` on a file stream that is already open will fail and set `failbit`. 
Subsequent attempts to use that file stream will fail. To change the associated file, we would have to close then open a new one: 
```
in.close(); // close the file 
in.open(ifile + "2");
```


#### Automatic Construction and Destruction
Considering a program whose `main` function takes a list of files it should process. 
It might have a loop that looks like this: 
```
// for each file passed to the program 
for (auto p = argv + 1;p != argv + argc; ++p){ 
	ifstream input(*p); // create input and open the file
	if(input){ 
		process(input);
	} else
		cerr << "Couldn't open: " + string(*p);
};// input goes out of scope and is destroyed on each iteration
```

Each iteration constructs a new `ifstream` object named `input` and opens it to read the given file. 
We check the open succeeded, which is a thing you should regular do. 
When an `fstream` object goes out of scope, the file it is bound to is automatically closed. `input` is then created anew. 
When destroyed, close is called automatically. 

### File Modes
#filemodes
Each stream has an associated **file mode** that represents how the file may be used. ![[Pasted image 20240313024654.png]]
Whenever we open a file, we give a file mode, either when we casll `open` or when we indirectly open the file when we initialize a stream from a file name. 
The modes that we can specify have the following restrictions: 
- `out` only for `ofstream` or `fstream` object
- `in` may be set only for an `ifstream` and `fstream` object
- `trunc` only set when `out` is specified too. 
- `app` mode is fine if `trunc` is not there too. When this is specified, the file is always opened in output mode, even if `out` was not supplied. 
- By default, `out` mode is truncated even if we do not specify `trunc`. To preserve the contents of the file opened with `out`, either we must also specify `app`, in which case we can only write to the end of the file, or we must also specify `in`, in which case the file is open for both (in and out), we will further cover this later, and how we might use such a file. 
- The `ate` and `binary` modes may be specified on any file stream object type and in combination with any of the other file modes. 
Each file stream type defines a default file mode that is used whenever we do not otherwise specify a mode. 
Files associated with an `ifstream` are opened in `in` mode; files associated with an `ofstream` are opened in `out` mode. 
Files associated with `fstream` are opened in both `in` and `out` modes. 

### Opening a File in Out mode Discards Existing Data
As in out to the file, we output to the file, not out to the file to my output stream to console window. 

BY DEFAULT, when we open an `ofstream`, the contents of the file are discarded. 
The only way to stop this is to specify `app`. 

```
//file 1 is truncated in each of these cases
ofstream out("file1"); // out and truncate are implicit
ofstream out2("file1", ofstream::out); // trunc is implicit, out is explicit
ofstream out3("file1", ofstream::out | ofstream::trunc); // both explicit

// preserving the file's contents
ofstream app("file2", ofstream::app); // out is implicit, append is explicit
ofstream app2("file2", ofstream::out | ofstream::app);
```
The only way to preserve existing data in a file opened by an `ofstream` is to specify `app` or `in` mode explicitly. 

`trunc` removes data in the existing file

### File mode is Determined Each time `open` is Called
The file mode may change each time it is opened: 
```
ofstream out; // no file mode is set
out.open("scratchpad"); // mode implicitly out and trunc
out.close(); // closing out so we can use it for a different file
out.open("precious", ofstream::app); // appends all the outputs at the end!
out.close();
```
Remember that implicitly `out` also means `trunc`. 

This bit: 
`ofstream app2("file2", ofstream::out | ofstream::app);`
looks a little confusing, however, it's just a bitwise operator. 
So imagine the likes of `out = 1 // 0001` and `app = 2 // 0010` 
Then when we or them `0001 | 0010 we get 0011`, even if we XOR then we get the same thing. 

