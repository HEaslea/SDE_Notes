Implementing a simple text-query program. 
User search a given file for words that might occur in it. 
Result -> number of times the word occurs and a list of lines on which that word appears. If word in line > 1, display line only once. Output of lines in ascending order. 

eg. 
![[Pasted image 20240410161908.png]]

##### Design
- When reading input, remember the line for which the word appears. Reading input $\therefore$ a line at a time. Check each word individually. 
- When it generates output: 
	- Fetch line numbers associated with a given word
	- Ascending order with no duplicates
	- Output text from file appearing in the input file at a given line number

These can be met quite neatly using various library facilities: 
- Using a `vector<string>` to store a copy of the entire input file. Each line as an element. Line number therefore will == `index`. 
- Using `istringstream` to break each line into words, easy. 
- Using a `set` to hold the line numbers on which each word in the input appears. Guaranteeing each line appears once, and they are stored in ascending order, no matter insertion point, into set. 
- Using a `map` to associate each word with the with the `set` for any given word. 

There will also be `shared_ptr`s, very fun indeed. 

The first thing we want to do is create a class, (more abstract solution than what might be necessary), making it easier to query our file. 
We'll call this `TextQuery`, somewhat close to a singleton, which will hold a `vector` and a `map`.
`vector` holding the text of the input file; map will associate each word in that file to the set of line numbers on which that word appears. 
The constructor will be that which feeds the vector the file input. 
We'll give it an operation to perform queries; look inside `map` see whether the given word is present. The best return type for this is actually another class, holding multiple things to return at once. `QueryResult` to hold these. This itself will have a `print` function. 


### Sharing Data Between Classes
This will just be done with `shared_ptr`s, so that we know that data does exist, or that we can use it freely etc. 


#### Using the `TextQuery` Class
It is usually helpful to write programs using the class before actually implementing the members:
This is using both classes that we want to implement later on. 
```
void runQueries(ifstream &infile)
{ 
	// infile is an ifstream that is the file we want to query
	TextQuery tq(infile); // store the file and build the query map her
	// iterate with the user: prompt for a word to find and print results
	while(true){ 
		cout << "Enter the word to look for, or q to quit: ";
		string s; 
		// stop if we hit eof on the input or if a 'q' is entered
		if(!(cin >> s) || s == "q") break;
		print(cout, tq.query(s)) << endl;
	}
}
```
We initialize a `TextQuery` object named `tq` from a given `ifstream`. 
The `TextQuery` constructor reads that file into its `vector` and builds the `map` that associates the words in the input with the line numbers on which they appear. Does this as the constructor (I think, if I read the thing from earlier correctly). 


##### Defining `TextQuery` Class
```
class QueryResult; // we use this class in this class, declaration beforehand
class TextQuery{ 
public: 
	using line_no = std::vector<string>::size_type; 
	TextQuery(std::ifstream&);
	QueryResult query(const std::string&) const;
private: 
	std::shared_ptr<std::vector<std::string>> file; // input file
	// map of each word to the set of the lines in which that word appears
	std::map<std::string, std::shared_ptr<std::set<line_no>>> wm;
}
```

`map<string, shared_ptr<set<line_no>>> wm;`
Is easier to read, however, in header files, we don't want to use `using namespace std;`
###### The constructor 
```
// read the input file and build the map of lines to line numbers
TextQuery::TextQuery(ifstream &is): file(new vector<string>)
{ 
	string text; 
	while(getline(is, text)){  // for each line of the file
		file->push_back(text); // remember this line of text
		int n = file->size() - 1; // the current line number
		istringstream line(text); // separate the line into words
		// using a string stream
		while(line >> word){ 
			// if word isn't already in wm, subscripting adds a new
			// entry
			auto &lines = wm[word]; // lines is a shared_ptr
			// this will be pointing to a set of line_no
			if(!lines) // that pointer is null the first time we see
			// word
				lines.reset(new set<line_no>); allocate a new set
			lines->insert(n); // insert this line number
		}
	}
}
```

We initialize a new vector to hold the text from the input file. 
Use `getline` to read the file a line at a time and push each line onto the `vector`. 

Then, using `istringstream`, processing each word at a time. The inner `while` uses the `istringstream` input operator to reach each word, then we use the `map` subscript operator to fetch the `shared_ptr<set>` associated with the `word` and bind `lines` to that pointer. 
Note here that `lines` is a reference so changes made to `lines` will be made to the element in `wm`.

If we are adding a `set` for the first time, that will be value initialized to a nullptr, therefore that's why we check, and make sure that we allocate a `new set` and call `reset` to update the `shared_ptr` to which `lines` refers to. 

`lines` is a reference, the call to `insert` adds an element to the `set` in `wm`. 
If given word occurs more than once in the same line, the call to `insert` does nothing. 


###### The `QueryResult` class
This class has three data members: a `string` that is the word whose results it represents; a `shared_ptr` to the `vector` containing the input file; and a `shared_ptr` to the `set` of line numbers on which this word appears. 

Its only member function is the member construction. 
```
class QueryResult{ 
friend std::ostream& print(std::ostream&, const QueryResult&);
public: 
	QueryResult(std::string s, 
				std::shared_ptr<std::set<line_no>> p,
				std::shared_ptr<std::vector<std::string>> f): 
		sought (s), lines(p), file(f) {}

private: 
	std::sring sought; // word this query represents
	std::shared_ptr<std::set<line_no>> lines; // lines it's on
	std::shared_ptr<std::vector<std::string>> file; // input file
};
```

###### The `query` Function
This takes a `string`, which it uses to locate the corresponding `set` of line numbers in the `map`. 
If found, the `query` function constructs a `QueryResult` from the given `string`, the `TextQuery file` member, and the `set` that was fetched from `wm`. 

What do we return if the `string` is not found. There is no `set` to return in this case. 
We solve this by defining a local `static` object that is a `shared_ptr` to an empty set of line numbers. When the string is not found, we'll return a copy of this `shared_ptr`. 

```
QueryResult
TextQuery::query(const string& sought) const
{ 
	// we'll return a pointer to this set if we don't find sought
	static shared_ptr<set<line_no>> nodata(new set<line_no>);

	// use find and not a subscript to avoid adding words to wm!
	auto loc = wm.find(sought);
	if(loc == wm.end()) // using a alg means we can do this too
		return QueryResult(sought, nodata, file); // not found
	else
		return QueryResult(sought, loc->second, file);
}
```

###### Printing the Results
The `print` function prints its given `QueryResult` object on its given stream. 

```
ostream &print(ostream& os, const QueryResult &qr)
{ 
	// if the word was found, print the count and all the occurrences
	os << qr.sought << " occurs " << qr.lines->size() << "" << make_plural(qr.lines->size(), "time", "s") << endl;
	// print each line in which the word appeared
	for(auto num : *qr.lines) // for every element in the set
		// don't confound the user with th text lines starting at 0
		os << "\t(line" << num + 1 << ")" << *(qr.file->begin() + num) << endl;
	return os;
}
```

We use the line number to fetch a line from the vector to which `file` points. 
Remember that `file->begin() + num` is the `num`th element after the start of the `vector` to which `file` points. 
Eg.
```
vector<string> vs = {"Hugo", "Tom", "Martin"};
cout << *(vs.begin() + 1); // output is Tom
```

That's about it, using a lot of what that chapter talked about, probably worth going over it again, so that you know some of the specifics that you feel you have missed. 

