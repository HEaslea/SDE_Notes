#reverseiterators
[[Iterators]] [[Iterators Revisited]] [[Insert Iterators]]
Will traverse backwards through the container. 
It will invert the meaning of increment and decrement. 
`++it` will move "backwards", towards the beginning of the list. 

All containers, bar `forward_list`, all have reverse iterators. 
In order to get one, we have to use the `rbegin, rend, crbegin, and crend`. 
Remember here that we will have one past the beginning. 
![[Pasted image 20240326155340.png]]
Comparing `begin/cend` and `rbegin/crend` iterators: 
```
vector<int> vec = {0,1,2,3,4,5,6,7,8,9};
// reverse iterator of vector from back to front
for(auto r_iter = vec.crbegin(); // binds r_iter to the last element
	r_iter != vec.crend(); // crend refers 1 before 1st element
	++r_iter) // "Decrements" the pointer one towards the beginning
	cout << *r_iter << endl; // prints 9,8,7,...0
```

Although it may seem confusing to have the meaning of the increment do what it does here, doing so lets us use the algorithms that we have usead before to process a container forward and backward, if we have to use -- to move one towards the beginning with the reverse iterators, then there would literally be no point in using reverse iterators lol; 
You could just decrement through a container using normal iterators, however, you would have to say something along the lines of: 
```
auto it = vec.end(); --it;
```
It would have to look something like this: 
```
vector<int> ni = {0,1,2,3,4,5,6};
auto it = ni.end(); it--;
auto bit = ni.begin(); bit--;
while(it != bit) { cout << *it << endl; --it; }
```


```
sort(vec.begin(), vec.end()); // sorts vec in "normal" order
// sorts in reverse; puts the smallest element at the end of vec
sort(vec.rbegin(), vec.rend());
```

#### Reverse iterators require Decrement Operators
Remember here that stream iterators do not use decrement, as you cannot move backwards through a stream. That is why we cannot use an iterator for `forward_list` and or stream iterator. 

### Relationship Between Reverse Iterators and Other Iterators
#iteratorswithreverseiterators
Suppose that we have a `string` named `line` that contains a comma-separated list of words,  and we want to print the first word in `line`. 
Using `find` this task is super easy: 
```
string line = "hello there, how are you doing";
auto comma = find(line.cbegin(), line.cend(), ',');
cout << string(line.cbegin(), comma) << endl;
```

If we wanted the last word, then we would just use the reverse iterator: 
```
auto rcomma = find(line.crbegin(), line.crend(), ',');
```

However, if we were to do: 
`cout << string(line.crbegin(), rcomma) << endl;` then we would get the word in the wrong order. 
![[Pasted image 20240326164536.png]]
What we really need to do is transform `rcomma` back to being an ordinary iterator that will go forward through `line`. 
#base
We can do this by using the `base` member, giving us a corresponding ordinary iterator:  

```
// ok get a foward iterator and read to the end of the file
cout << string(rcomma.base(), line.cend()) << endl;
```
See that the base will do what we need and increment towards the end of the string in this case, so that we can read out what we need. 

### Reverse and Ordinary Iterators
#iteratorswithreverseiterators 

Given the same preceding input, this statement prints LAST as expected. 
The big thing here is that `rcomma` and `rcomma.base()` will refer to different things. 
As do `line.crbegin()` and `line.cend()`. 
This is necessary in order to make the *range* of elements, whether processed forward or backward, is the same. 

They accommodate for the left-inclusive range. 
The point is this: 
`[line.crbegin(), rcomma)` and `[rcomma.base(), line.cend())` refer to the same elements in `line`. 
Just remember this using this idea here: 

```
string name = "Hugo";
auto rcom = name.crbegin();
cout << *rcom << endl; // o
rcom++;
cout << *rcom << endl; // g
auto base = rcom.base();
cout << *base << endl; // o
```

