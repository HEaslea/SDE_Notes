[Cool Video About Algorithms](https://www.youtube.com/watch?v=2olsGf6JIkU)
#generalalgorithms
This library has a surprisingly small set of operations. 
Rather than adding lots of functionality to each container, the library provides a set of algorithms, most of which are independent of any particular container.

They are general and *generic*; operating on different types of containers and on elements of various types. 
We'll have a look at iterators in more detail as well. 

*sequential containers* define few operations; for the most part, we can add, remove elements, access the first or last element etc. etc. 
We might want to do a whole host of other things, that are not defined in those libraries. 

Most of these are defined in `algorithm` header. 
There are also some in the `numeric` header as well. 
They do not generally work on the container,  however, using iterators, or traversing between two iterators and the range in-between them. 
Imagine if we had a vector if ints, and we want to know if the vector holds a particular int. The easiest way to answer this is to call the library `find` algorithm. 
```
int val = 42; // value we'll look for
// result will denote the element we want if it's in vec, or vec.end() if not
auto result = find(vec.begin(), vec.end(), val); // giving a range
// report the result
cout << "The value " << val << (result == vec.end() ? "is not present" : " is present") << endl;
```

The first two arguments here are the two iterators , providing a range of elements, and the third is the value. 
`find` will compare each element in the given range to the given value. 
If there is no match, then `find` returns the second iterator to indicate failure. 
Thsu we can determine whether the element was found by comparing the return value with the second iterator value. 

```
string val = "a value";// value we'll look for
// this call to find looks through string elements in a list
auto result = find(lst.cbegin(), lst.end(), val);
```
Because pointers **act** like iterators on built-in arrays, we can use `find` to look in an array too: 
```
int ia[] = {23,234,345,324,2,345};
int val = 83; 
int *result = find(begin(ia), end(ia), val);
// std::find(thing, thingend, val); remember the std::
// this is fine as well as ia is a pointer to array when used like this
int *result = find(ia, std::end(ia), val);
```
Looking at a subrange of the sequence by passing iterators to the first and one past the last element of that subrange. 
```
// search the lemeents starting from ia[1] up to but not including ia[4]
auto result = find(ia + 1, ia + 4, val);
```
granted that `ia` decays into a pointer implicitly here. 

### How the Algorithms Work
