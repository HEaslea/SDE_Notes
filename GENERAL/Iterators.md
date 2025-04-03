#iterators
[[Iterators Revisited]]
```
auto b = v.begin(), auto e = v.end();
```
end() will return the "off the end" element position. It doesn't exist. 
If the container is empty, then the end position will = the beginning position. 
In this sense they will both be off-the-end iterators. 

Iterators only support a few operations, like comparing too with `==` or `!=`. They will be equal if they denote the same element or if they are both off-the-end iterators for the same container. 
Otherwise they are unequal. 
![[Pasted image 20240212000646.png]]

```
string s("some string");
if(s.begin()!=s.end()){ // make sure it's not empty
	auto it = s.begin(); // s denotes the first character in s
	*it = toupper(*it);
}
else{
cout << "String is empty"; // if it is empty
}
cout << s << endl;
```
`*it` dereferences the iterator, meaning that we access the element that it has position over. 

Moving iterators from one element to another: Using the ++ (increment) to move from one element to the next. 
"Advance the iterator by one position".

`end()`would obviously not able to be incremented or dereferenced. 

```
for(auto it = s.begin(); it != s.end() && !isspace(*it); ++it){ 
	*it = toupper(*it)} // capitalize the current character
```

We will mainly use `==` and `!=`, as they are defined for iterators in most cases. 

We generally do not need to know the precise type of an iterator. 

```
vector<int>::iterator it; // it can read and write vector<int> elements
string::iterator it2; // it2 can read and write characters in a string 

vector<int>::const_iterator it3; // it3 can read but not write elements
string::const_iterator it4; // it4 can read but not write elements too
```

`const_iterator` behaves like a `const` pointer. 

If `vector` or `string` is a `const`, then we have to use the `const_iterator` type. 

Three different iterator entities: - the concept - the type defined by a container - or the iterator object. 

The `end(),  begin()` will return `const_iterator` if the element they are on is a `const` type as well. 

Obviously if they are not `const`, then they will not return `const_iterator` and just regular `iterator`.

```
vector<int> v;
const vector<int> cv;
auto it1 = v.begin(); // it1 has type vector<int>::iterator
auto it2 = cv.being(); // it2 has type vector<int>::const_iterator
```

We can also use `cbegin() and cend()`, so that we can choose `const_iterator` even when the container is not const. 

The idea of `(*it).empty()` to see if the element is empty. The parentheses are necessary around the `*it` as that means that the dot empty will apply to the answer of `*it`.
`*it.empty()`, this means that we want to fetch the member named empty around it. 

The arrow operator does combine the idea of dereferencing and member access into a single operation. That is `it->mem` is a synonym for `(*it).mem`.

Say if we had a `vector<string>` named `text` that holds the data from a text file. 

Each element is either a sentence or an empty string to represent a paragraph break. 
To write a loop that would iterate through text until we find an empty: 
```
for(auto i = text.cbegin(); it != text.cend() && !it->empty(); ++it){ 
cout << *it << endl;}
```

Try not to add elements to containers that you are utilizing iterators for. 

![[Pasted image 20240212005301.png]]
```
// compute an iterator to the element closes to the midpoint of vi
auto mi = vi.begin() + vi.size()/2;
```
Of course if vi has 20 elements, then the `vi.size()/2` is 10. 
We would set `mid` equal to `vi.begin() + 10`. 

If we subtract two iterators, then that will return a `difference_type` as this has to be signed, as it could be a negative. 

And finally; the binary search using iterators; 

```
// remember that our elements must be sorted
// beg and end will dnote the ranger we're searching
auto beg = text.begin(), end = text.end();
auto mid = text.begin() + (end - beg)/2; // original mid point.
// while there are still elements to lok at and we haven't yet found sought
while(mid != end && *mid != sought{
	if(sought < *mid){ 
		end = mid}
	else{ 
		beg = mid + 1} // start looking with the element just after mid
	mid = beg + (end - beg)/2; // new midpoint
}
```


[[Sequential Containers]]
[[Overview of Sequential Containers]]

Operations with iterators will all do the same thing for each iterator. No matter the container, they all let us access and element by providing an dereference operator. 
Such as the -- and ++ in order to move to the next or last element, yet this will only apply to containers such as `string, vector, deque and array`. The others you might have to define. 

### Iterator Ranges
Denoted by a pair of iterators each of which refers to an element, or one past the last element, in the same container. `begin` and `end`, as `first` and `last` mark a range of elements from the container. 
Although `last` is commonly used, is a bit misleading, the second iterator never refers to the last element in range, rather, it points to one past the last element. 
Therefore we have `first` up to, but not including `last` in order to include all our elements in our range. 


The element range looks something like this, which is a left inclusive interval. 
The standard math notation for this is `[begin, end)`, duh. 
The iterators must refer to the same container.
The iterator `end`, may be equal to `begin` but must not refer to an element before the one denoted by `begin`.

Requirement on Iterators forming an iterator Range: 
two iterators `begin`, and `end` form an iterator range, if: 
- they refer to elements of, or one past the end of, the same container, 
- it is possible to reach `end` by repeatedly incrementing `begin`. `end` must not therefore precede `begin`.

The compiler cannot and will not enforce these requirements. It is up to us to ensure tha tour programs follow these conventions. 

### The Program Implications of Using Left-Inclusive Ranges
These ranges have three convenient properties. 
If the range is valid: 
- if `begin` equals `end`, then the range is empty (not one element, literally empty set)
- if `begin` is != `end`, there is at least one element in the range, and `begin` will refer to it and `end` will not.
- We can increment `begin` some number of times until `begin == end;`


We can therefore safely write lops such as the following to process a range of elements: 
```
while( begin ! = end ) { 
	*begin = val; // ok as range isn't empty so begin does denote an element
	++begin; // duh
}
```

Remember, when we have a while loop that we should always be looking to see whether the loop actually terminates or not. 

