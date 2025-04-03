```
string sentence; 
std::getline(cin, sentence);
```

That is the easiest way of doing it. 
The great way of doing this is that it will just read one line, therefore, as soon as you press enter, you move on. 

Here is another cool way of doing it like we mentioned earlier. 
```
string sentence, add;
while(cin >> add){ 
	sentence += (add + " ")
}
```
however, with this one, you have to have a way of breaking out of `cin >> add`. 
This is great if you are going to put in multiple lines after hitting enter, as it will just add everything to the sentence. 


This last one is a little weird due to the size issue, as if you don't have enough input data to cover the size of `buffersize` then it will just fill it with random elements: 
```
const int buffersize = 100; 
char buffer[buffersize];
cin.getline(buffer, buffersize);
for(char c : buffer){ 
	cout << c;
}
cout << endl;
```
Which is great if you know the size of the input: 
`Hello there how are you` will result in: 
![[Pasted image 20240323013013.png]]
like wtaf is that lol. 

The `getline` one is probs the best for what we want to do. 



### Getting Chars from cin

#whatiscin
The general idea is that cin is a buffer that holds the input, therefore when I write `Hello World`, into the console, it will just go into cin. 
It can then be accessed char by char, like how strings are. 

```
char c; 
while(cin.get(c)){ 
	cout << static_cast<char>(toupper(c));
}
cout << endl;
```


### Creating a Vector from range of cin iterators
```
istream_iterator<int> in_iter(cin), eof;
vector<int> vec(in_iter, eof);
for(auto i : vec){ 
	cout << i << " : ";
}
cout << endl;
```