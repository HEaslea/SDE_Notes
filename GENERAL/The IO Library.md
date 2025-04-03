#cpplibrary #theiolibrary

C++ doesn't like input and output directly. 
IO is handled by a family of types defined in the standard library. 
They support IO from devices like files and console windows. 

We have already used a lot of this library however, we'll go more in-depth here. 

What we have seen so far is: 
- `istream` (input stream) **type**, providing input operations. 
- `ostream` (output stream) **type**, providing output operations
- `cin`, an`istream` object that reads the standard input
- `cout`, an `ostream` object that writes to the standard output (whatever that means lol). 
- `cerr`, an `ostream` object, typically used for program error messages, that writes to the standard error. 
- The >> operator, used to read input from an `istream` object.
- the << operator, used to output to an `ostream` object. 
- The `getline` function, which reads a line of input from a given `istream` into a given `string`. 

## The IO Classes
#ioclasses
The IO types so far manipulate `char` data. 
**By default these are connected to the user's console window**. 
Real programs (yikes loser) cannot be limited to doing IO solely to or from a console window. 
Programs often need to write and read from named files. 
Applications also may have to read and write languages that require wide-character support. 
The library will define a collection of IO types in addition to the `istream` and `ostream` types. 
`iostream` defines the basic type used to read from and write to a stream. 
`fstream` defines the types used to read and write named files. 
`sstream` defines types used to read and write in-memory `string`s.

![[Pasted image 20240311145517.png]]

In order to deal with `wchar_t`, we define. They will all begin with `w`, so `wcin`, `wcout`, and `wcerr` are wide-character objects that correspond to `cin`, `cout` and `cerr`.
They are defined in the same header as the plain `char` types. 
The `fstream` header defines both the `ifstream` and `wifstream` types. 

### Relationships among the IO Types
#relationshipsio
We'd like to use `>>` to read data regardless of whether we're reading a console window, a disk file, or a `string`. 

The library lets us ignore the differences among these different kinds of streams by using `inheritance`. Which will be covered later on. 

Types `ifstream` and `istringstream` inherit from `istream`. 
You know roughly how it works based on Python inheritance too. 

### No Copy or Assign for IO Objects
We as users, cannot assign objects of the IO types: 
```
ofstream out1, out2; //fine but these are incomplete types with just this line of code.
out1 = out2; // error cannot assign stream objects
ofstream print(ofstream); // error cannot initialize the ofstream parameter
out2 = print(out2); // cannot copy stream objects
```
We cannot therefore have a parameter or a return type that is one of the stream types. 
Functions that do do this will return references to one of those objects. Reading or writing an IO object will change its state, therefore the reference cannot be `const`. 


#### Condition States
#conditionstates
Errors can occur in IO, some are recoverable and others occur deep within the system and are beyond the scope of a program to correct. . 
The IO classes define functions and flags, listed here, that let us access and manipulate the **condition state** of a stream. 
![[Pasted image 20240311155548.png]]

So imagine if we did something like this: 
```
int ival;
cin >> ival;
```

If we then enter `Boo` , the read will fail. 
The input operator expected to read an `int` but got the character `B` instead. 
`cin` will be put into an error state. 
Once the error has occurred, then the next operations will also fail. 
We can only read and write to a stream when it is in a non-error state. 

We should ordinarily check whether the stream is okay before attempting to use it. 
The easiest way to determine that would be in a condition check: 
```
while (cin >> word) // ok; read operation successful
```
The while condition will check the state of the stream returned from the >> expression. If that input operation succeeds, the state remains valid and the condition will succeed. 

### Interrogating the State of a Stream 
#interrogatingstateofstream
This only tells us whether the stream is valid. 
Doesn't tell us what happened. 
We would need to know why the stream is invalid too, changes our course of correction. 

The IO library names a integral type `iostate`, that is used to convey information about the state of the stream. 
Used as a collection of bits, in the same way that we used `quiz1` . 
The IO classes define four `constexpr` values of type `iostate`, that all represent particular bit patterns.
These values indicate particular kinds of IO conditions. 
They can be used with the bitwise operator to test or set multiple flags in one operation. 

`badbit` indicates a system-level failure, such as an unrecoverable read or write error. 
Usually can't use a stream if the `badbit` is set. 
The `failbit` is set after a recoverable error, such as reading a character when numeric data was expected. 
Often able to correct and continue using the stream. 
Reaching EOF sets the `eofbit` shock. 
`goodbit` is guaranteed to have value 0, indicating no failures on stream. 
If any `badbit failbit eofbit` are set, then a condition that evaluates that stream will fail. 

The library will also define functions to interrogate the state of these flags. 
The `good` operation returns true if non of the error bits are set. 
Then there's `bad, fail, eof`, returning true if their bit is set. 
`fail` will return true if the `badbit` is set. 

#### Managing Condition State
The `rdstate` member returns an `iostate` value that corresponds to the current state of the stream. 
The `setstate` turns on the given condition bit(s) to indicate that a problem occurred. 
The `clear` member is overloaded: one version takes no arguments and a second version takes a single argument of type `iostate`; no args turns off all the failure bits. After `clear()` a call to `good` returns `true`. 
Here is an example: 
```
// remember the current state of cin 
auto old_state = cin.rdstate(); / remember the current state of cin 
cin.clear(); // make cin valid again 
process_input(cin); // use cin
cin.setstate(old_state); // now reset cin to its old state
```

The version of `clear` that takes an argument expects an `iostate` value that represents the new state of the stream. 
To turn off a single condition, we use the `rdstate` member and the bitwise operators to produce the desired new state. 

The following turns off the `failbit` and `badbit` but leaves `eofbit` untouched: 
```
// turns ofof failbit and badbit, all other bits unchanged
cin.clear(cin.rdstate() & ~cin.failbit & ~cin.badbit);
```

The brackets here are a bit pattern (well, isn't everything?) therefore we are clearing. 
Remember that 0 will indicate that there is no error with the state that that bit represents. 
So here, we are clearing all the bits that are: 
- The first `cin.rdstate()`, will return a bit pattern, that we can use in `cin.clear()`. 
- Then we have NOT `failbit` AND NOT `badbit`. This is sort of a mask

#### Managing the Output Buffer
#outputbuffer
Each output streem manages a buffer, which it uses to hold the data that the program reads and writes. 
eg. when the following code is executed: 
`os << "Please enter a value";`

The literal string might be printed immediately, or the OS might store the data in a buffer to be printed later. 
Using a buffer allows the OS to combine several output operations from our program into a single system-level write. 
Writing to a device can be time consuming, letting the OS combine several output operations into a single write can provide an important performance boost. 

There are several conditions that cause the buffer to be flushed - that is, to be written - to the actual output device or file: 
- The program completes normally. All output buffers are flushed as part of the `return` from `main`.
- At some indeterminate time, the buffer can become full, in which case it will be flushed before writing the next value. 
- We can flush the buffer explicitly using a manipulator such as `endl`
- We can use `unitbuf` to set the stream's internal state to empty the buffer after each output operation. It's set for `cerr`, so that writes to `cerr` are flushed immediately. 
- Streams might be tied to another. Then that stream will be flushed whenever the stream to which it is tied to is read or written. `cin and cerr` are both tied to `cout`. Reading either will flush `cout`. 

### Flushing the Output Buffer
`flush` and `ends`: `flush` flushes the stream but adds no characters to the output, `ends` inserts a null character into the buffer and then flushes it: 
```
cout << "Hi" << endl; // newline print and flushes the buffer
cout << "Hi" << flush; // writes hi, then flushes the buffer; adding no data
cout << "Hi" << ends; // write hi and a null, then flushes the buffer
```

### `unitbuf` Manipulator
#unitbuf
Flushing after every output, we can use `unitbuf`. 
Tells the stream to do a flush after every subsequent write. 
The `nounitbuf` restores the stream to normal use, system managed buffer flushing. 
```
cout << unitbuf; // all writes will be flushed immediately
// any output is flushed immediately, no buffering
cout << nounitbuf; // returning to normal buffering
```
Here is a warning, buffers are not flushed if the program crashes. 

### Tying Input and Output Streams Together
When input stream is tied to an output stream, any attempt to read the input stream will first flush the buffer associated with the output stream. Like the library tying the `cout` and `cin` streams. 
The statement `cin >> ival;` causes the buffer associated with `cout` to be flushed. 
This would mean that when we want to take input from the user, we would flush all the output to the user. 

There are two overloaded versions of `tie`: 
one taking no arguments and returns a pointer to the the output stream, if any, to which this object is currently tied. 
`nullptr` if no tie is found. 

The second takes a pointer to an ostream, and ties itself to the ostream. 
This is `x.tie(&o)` ties the stream x to the output stream o. 
We can tie either an `istream` or an `ostream` to another `ostream`: 
```
cin.tie(&cout); // illustration only; the library ties cina nd cout for us
// old_tie points to the stream (if any) currently tied to cin
ostream *old_tie = cin.tie(nullptr); // cin is no longer tied
// ties cin and cerr; not a good idea because cin should be tied to cout moreso
cin.tie(old_tie); // reestablish normal tie between cin and cout
```

To tie a given stream to a new output stream, we pass `tie` a pointer to the new stream. To untie the stream completely, we pass a nullptr. Each stream can be tied to one stream at a time. 
However, multiple streams can tie themselves to the same `ostream`.


