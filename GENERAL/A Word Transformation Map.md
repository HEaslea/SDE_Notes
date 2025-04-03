#transformationmap
[[Associative Container]] 
#associativemcontainerexample
Writing a program to illustrate creating, searching and iterating across a `map`. 
The input will be from two files. 
Each rule consists of a word that might be in the input file and a phrase to use in its place. 
The idea being that whenever the first word appears in the input, we will replace it with the corresponding phrase. 
The second file contains the text to transform. 
If the contents of the word-transformation file are: 
```
brb be right back
k okay?
y why
r are
u you
pic picture
thk thanks!
l8r later
```

And the text that we are given to transform is: 
```
where r u
y dont u send me a pic
k thnk l8r
```

The program should eventually generate: 
```
where are you
why dont you send me a picture
okay? thanks! later
```

We are going to use three functions. 
the `word_transform` function, managing overall processing. It will take two `ifstream` arguments. 
The first bound to the word-transformation file and the second to the file of text we're to transform. 

We'll start by defining the `word_transform` function. 
The important parts are the calls to `buildMap` and `transform`. 
```
void word_transform(ifstream &map_file, ifstream &input){ 
	auto trans_map = buildMap(map_file); // store the transformations
	string text;
	while(getline(input, text)) { // read line of input (the ifstream)
		istringstream stream(text); // read each word
		string word;
		bool firstword = true; // controls whether a space is printed
		while(stream >> word){ 
			if(firstword)
				firstword = false;
			else
				cout << ""; // print a space betwen words
			cout << transform(word, trans_map); // print the output
		}
	}
}
```
You can see that the function starts by calling the `buildMap` to generate the word transformation `map`. 
We store the result in `trans_map`. The rest of the function processes the `input` file. 
The `while` loop here uses `getline` t read the input file a line at a time. 
We read by line so tha tour output will have line breaks at the same position as in the input file. 
To get words from each line, we use a nested `while`loop that uses an `istringstream` to process each word in the current line. 

The inner `while` prints output using the `bool firstword` to determine whether to print a space. 
The call to `transform` obtains the word to print. 
The value returned from `transform` is either the original `string` in `word` or its corresponding transformation from `trans_map`. 

### Building the Transformation Map
The `buildMap` function will read its given file and builds the transformation `map`. 
```
map<string, string> buildMap(ifstream &map_file){ 
	map<string, string> trans_map; // holds the transformations
	string key; // a word to transform
	string value; // phrase to use isntead
	// read the first word into key and the rest of the line into value
	while(map_file >> key && getline(map_file, value))
		if(value.size()>1) // check that there is a transformation
			trans_map[key] = value.substr(1); // skip leading space
		else
			throw runtme_error("no rule for" + key);
	return trans_map
}
```

Each line in `map_file` corresponds to a rule. 
Each rule is a word followed by a phrase, which might contain multiple words. 

Using `>>` to read th word that we will transform, into `key` and call `getline` to read the rest of the line into `value. 
`getline` doesn't skip leading spaces, we need to skip the space between the word and its corresponding rule. 
Before we store the transformation, we check that we got more than one character. 
If so, we call `substr` to skip the space that separated the transformation phrase from its corresponding word and store that substring in `trans_map`.
We use the subscript operator add the key-value pairs. 
Implicitly, we are ignoring what should happen if a word appears more than once in our transformation file. If a word does appear multiple times, our loops will put the last corresponding phrase into `trans_map`. 
When the `while` concludes, `trans_map` contains that data that we need to transform the input. 

### Generating a Transformation
The `transform` function does the actual transforming. 
Its parameters are references to the string to transform and to the transformation `map`. 
If the given string is in the `map`, `transform` will return the corresponding transformation. 
If the given `string` is not in the `map`, `transform` returns its argument: 
```
const string&
transform(const string &s, const map<string, string> &m)
{
	// the actual map work; this part is the heart of the program
	auto map_it = m.find(s);
	// if this word is in the transformation map
	if(auto != m.cend())
		return map_it->second; // return the replacement word
	else
		return s; // otherwise return the original unchanged
}
```

We star by calling `find` to determine whether the given `string` is in `map`. If it is, then `find` return the iterator to the corresponding element. 
When found, we dereference the iterator, obtaining a `pair` that holds the key and value for that lemeent. 
We return the `second` member, which is the transformed version of the word. 

