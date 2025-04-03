#iostreamiterators
[[Iterators]] [[Iterators Revisited]]
[[Insert Iterators]]

Even though `iostream` types are not containers as such, there are iterators that can be used with objects of the IO types. 
`istream_iterators` will read input streams, and `ostream_iterators` will read output streams of course. 

The iterators will therefore treat their corresponding stream as a sequence of elements of a specified type. 
Using stream iterators, we can use general algorithms to read data from or write data to stream objects. 

![[Pasted image 20240325135717.png]]

##### Operations on `istream_iterators`
When we create a stream iterator, we must specify the type of objects that the iterator will read and write. 
An `istream_iterator` uses >> to read a stream. 
Therefore, the type that an `istream_iterator` reads must have an input operator defined. 
When we create one, we can bind it to a stream. 
Alternatively, we can default initialize the iterator, which creates an iterator that we can use the off-the-end value. 
```
istream_iterator<int> int_it(cin); // reads ints from cin
istream_iterator<int> int_eof; // end iterator value, as defaul initialization
ifstream in("afile");
istream_iterator<string> str_it(in); // reads strings from "afile"
```

We can use an `istream_iterator` read the standard input into a vector: 
```
istream_iterator<int> in_iter(cin);; // reads ints from cin
istream_iterator<int> eof; // istream "end" iterator
while(in_iter != eof) // while there's valid input to read
// postfix increment reads the stream and returns the old value of the iterator
// we dereference that iterator to get th eprevious value read from the stream
	vec.push_back(*in_iter++);
```

If we have something like: 
`istream_iterator<int> in_iter(cin);`
This means that the iterator is geared up to reading ints from the buffer that is cin. 

```
istream_iterator<int> in_iter(cin), eof; // reads ints from cin
// remember that default definition will point to the end of cin
vector<int> vec(int_iter, eof); // construct vec from an iterator range
```


Just so you remember: 
that if you do something like this: 
```
vector<string> svec = {"Hugo", "Ted", "Martin", "Shrek"};
vector<string> nvec(svec.begin() + 2, svec.end());
// nvec will be equal to `Martin`, `Shrek`
```

Remember that if we have something like 
`istream_iterator<int> in_iter(cin);` that will be a pointer to the first one 
So if we do something like this after the code written about iterators above: 
`cout << *in_iter << endl;` with the input 1 2 3 4 5 etc., then we will output 1. 
The constructor reads `cin` until it hits EOF, or encounters an input that is not an `int`. 
Then these are obviously used for the vector of ints. 

### Using Stream Iterators with the Algorithms
Algorithms work in terms of iterator operations, and the stream iterators support at least some iterator operations, we can use stream iterators with at least some of the algorithms. 
We'll see later how we can tell which ones do and don't. 
Eg. we can call `accumulate` with a pair of `istream_iterators`
```
istream_iterator<int> in(cin), eof;
cout << accumulate(in, eof, 0) << endl;
```

If we had the input of: 
`23 109 45 89 6 34 12 90 34 23 56 23 8 89 23`
we would get the output of 664. 

### `istream_iterators` Are Permitted to Use Lazy Evaluation
When we bind `istream_iterator` to a stream, it is not guaranteed that it will read the stream immediately. 
The implementation is permitted to delay reading the stream until we use the iterator. 
We are guaranteed that before we dereference the iterator for the first time, the stream will have been read. 
For most programs, the read being a little delayed, doesn't matter. 
However, if we create an `istream_iterator` that we destroy without using or if we are synchronizing reads to the same stream from two different objects, then we might care a great deal when the read happens. 

### Operations on `ostream_iterators`
An `ostream_iterator` can be defined for any type that has an output operator (the << ).
When we create one, we may provide a second argument that specifies the char string to print following each element. 
The string must a c-style char string (ie. a string literal or a pointer to a null-terminated array). 
We must bind an `ostream_iterator` to a specific stream. There is no empty or off-the-end `ostream_iterator`. 

![[Pasted image 20240326004618.png]]

```
string nstring = "Hello there how are you", word;
stringstream newos(nstring);
vector<string> svec; 
while(newos >> word){ 
	svec.push_back(word);
}
ostream_iterator<string> out_iter(cout, " :NEW WORD: ");
for(auto e : svec){ 
	*out_iter++ = e;
}
cout << endl;
```
![[Pasted image 20240326005518.png]]
The program will write each element from `vec` onto `cout` following each element with a space. 
Each time we assign a value to `out_iter`, the write is committed. 
We can even omit the dereference and the incrementer too, makes it a little harder to read though: 
```
for(auto e : vec){ 
	out_iter = e; // the assignment writes the element to out
}
cout << endl;
```

The * and ++ operators do nothing on an `ostream_iterator`, so omitting them has no effect on our program. 
However, we prefer to write the loop as first presented. The loop uses the iterator consistently with how we use other iterator types. 

Rather than writing the loop ourselves, we can more easily print the elements in vec by calling copy. 
```
copy(vec.begin(), vec.end(), out_iter);
cout << endl;
```

### Using Stream Iterators with Class Types
We can create an `istream_iterator` for any type that has an input operator (>>).
 If we had something like `Sales_item` that we have been using, as it has >> and << operators. 
 We can use IO iterators to rewrite the bookstore program. 
```
istream_iterator<Sales_item> item_iter(cin), eof;
ostream_iterator<Sales_item> out_iter(cout, "\n");
// store the first transaction in sum and read the next record
Sales_item sum = *item_iter++;
while(item_iter != eof){ 
	// if the current transaction (which is stored in item_iter) has the same ISBN
	if(item_iter->isbn() == sum.isbn())
		sum += *item_iter++; // ad it to sum and read the next transaction
	else { 
		out_iter = sum; // write the current sum
		sum = *item_iter++; // read the next transaction
	}
}
```








The idea of outputting from stringstream, one char at a time: 
```
string nstring = "Hello there";
stringstream newss(nstring);
char c;
while(newss >> c){ cout << c << " : ";}
```

We will get: 
![[Pasted image 20240326010246.png]]