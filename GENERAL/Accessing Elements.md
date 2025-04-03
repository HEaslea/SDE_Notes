#accessingelements

![[Pasted image 20240316161302.png]]


#referencereturnusage
[[Reference Return Usage]]
Returning a reference is not like returning a pointer, if we say something like
`int f = c.front()`, assuming that `c` is a container with `int` element types, then it will return a copy, remember that we should not be returning a reference to a local variable. 
This example will copy the front element of the container. This doesn't seem clear, as so far we have seen the idea of `int f = getInt();`
However, if you look more clearly, you will see that `getInt();` will get a value and then `f` will be a copy of that value. If `getInt()` returns  a reference, it's still just an int, with a value somewhere, therefore `f` will just copy it.
However, if we declare `int &f = getInt();` here we are explicitly saying that f is a reference to the same value. 
This do also mean that we can get that rvalue, without having to reference to the actual lvalue, therefore we can take the value and change it without always changing the actual element. 
If this didn't work like that, then it woudl be incredibly awkward to get that rvalue so easily. This is a good way to think about how that would work, and why we have to use a reference denoter when accessing that element, to reference to it directly. 
`getInt()` returns a reference, the lvalue itself, be careful here remember, yet if we declare `f`, then we are copying, the return type just means that we aren't returning by copy. 
Don't worry so much about whether non-reference return types return a copy or an original, it doesn't matter, however, if we return a reference, we can actively use this. 
Here if we say `int &rf = c.front();` then we are saying that `rf` is a synonym for the first element in `c`.

Anyways, moving on, now that you understand that. 
If we have container `c`: 
```
if (!c.empty()){ 
	// these two are COPIES of the last element
	auto val = *c.begin();
	auto val2 = c.front();

	//these two are also COPIES of the last element in c
	auto last = c.end(); // one past last remember
	auto val3 = *(--last);  
	auto val4 = c.back(); // not supported by forward_list 
}
```

We can use iterators, and dereference them if we want to. 

`front and back` will be undefined if the container is empty. 

### Access Members Returning References
The members that access elements in a container, `front back subscript and at`, return references. 
If the container is a `const` object, the return is a reference to a `const`. 
```
if(!c.empty()){ 
	c.front() = 42;
	auto &v = c.back(); // get a reference to the last element
	v = 1024; // changes the element in c itself
	auto v2 = c.back(); // v2 is not a reference; it's a copy of the c.back() element itself
	v2 = 0; // no change to element in c
}
```
### Subscripting and Safe Random Access 
The containers that provide fast random access (`string, vector, deque and array`), will also provide the subscript operator. 
The index of the subscript obviously must be in range. It is up to the user to make sure this is the case. 
Using out of range index is a serious serious serious programming error, and if it is user defined, then oopsie. The compiler will not detect it, it will be a run-time error. 

In wanting to ensure the validity of our index, we can use the `at` member isntead. 
This will act like a subscript operator, but if the index is invalid, it throws an `out_of_range` exception. 

```
vector<string> svec; // empty vector=
cout << svec[0]; // run-time error; there are no element 
cout << svec.at(0); // throws an out_of_range exception
```
```
vector<string> svec; 
try{
	auto va = svec.at(0);
} catch (const std::out_of_range e){ 
	cout << "svec has no elements  : " << e.what();
}
```


### Erasing Elements
#erasingelements
![[Pasted image 20240317160140.png]]These members do not check their elements. 
The programmer must ensure that elements exist before removing them. 

### The `pop_front` and `pop_back` 
NO `pop_front` for `vector, string`, just as there is no `push_front`. 
`forward_list` does not have `pop_back`. 

These operations will return `void`. If you want the value that we are popping, then you would need to store that value before `pop`. 
```
while(!ilist.empty()){ 
	process(ilist.front()); // do something with the current top of ilist
	ilist.pop_front();
}
```

### Removing an Element within the Container
#removingelementsincontainer
The `erase` members remove element(s)t a specified point in the container. 
We can delete a single element denotes by an iterator or a range of elements marked by a pair of iterators. 

They both return an iterator referring to the location **AFTER** the (last) element that was removed. 
That is, if `j` is the element following `i`, then `erase(i)` will return an iterator referring to j.
Here is an example: 
```
list<int> lst = {0,1,2,3,4,5,6,7,8,9};
auto it = lst.begin(); 
whle(it != lst.end()){ 
	if (*it % 2) // if the element is odd
		it = lst.erase(it); // erase this element and our iterator is "incremented"
	else
		++it;
}
```

### Removing Multiple Elements
#removingmultipleelements
The iterator-pair version of `erase` lets us delete a range of elements: 
```
// delete the range of elements between two iterators
// returns an iterator to the element just after the last removed element
elem1 = slist.erase(elem1, elem2); // after the call elem1 == elem2
```
`elem1` refers to the first element we want to erase, and `elem2` refers to one past the last element we want to remove, therefore we get `elem1 == elem2` after the removal. 
This is done so that we can use `container.end();` without trying to delete an out_of_range element;

```
slist.clear(); // delete all elements within the container
slist.erase(slist.begin(), slist.end()); // equivalent
```