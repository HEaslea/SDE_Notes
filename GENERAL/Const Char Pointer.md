`cout`, if it takes a `char*`, then it will just print out the string, which is a behaviour from the `ostream` class. 

There are lots of weird shit that happen with using char, most of the time you can just assume that it acts as a string "sort of".
It will work in the same way here: 


```
char nstr[] = {"Hello World!"};
for(auto i : nstr){ 
	cout << i << endl;
}
```
Will print out the chars one line at a time. 
