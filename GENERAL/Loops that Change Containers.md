#containeroperationsinvalidatingiterators
[[Invalidating Iterators]]

We must cater to the idea of valid iterators. 

Therefore if we do anything to a container in a loop, we should probably refresh iterators as we go through. 
This is easy if the loop calls `insert or erase`. They return iterators, which we can use to reset the iterator that we have. 

```
// silly loop to remove even-valued elements and insert a duplicate of odd-valued elements
vector<int> vi = {0,1,2,3,4,5,6,7,8,9};
auto iter = vi.begin(); // call begin, not cbegin because we're changing vi
while(iter != vi.end()){ 
	if(*iter % 2){ // true if odd
		iter = vi.insert(iter, *iter); // duplicate the current element
		iter += 2; // advance past this element and the one inserted before it
	} else 
		iter = vi.erase(iter); // remove even elements
		// don't advance the iterator; iter denotes the element after the one we erase
}
```

### Avoid Storing the Iterator Returned from `end`
Consider a loop that processes each element and adds a new element following the original. 
We want the loop to ignore the added elements, and to process only the original elements. 
After each insertion, we'll position the iterator to denote the next original element. 
If we attempt to 'optimize' the `loop` by storing the iterator returned by `end()`, we'll have a disaster.

```
// disaster as behaviour of this loop is undefined
auto begin = v.begin(), 
	end = v.end(); // one past the end
while (begin != end){ 
	// do something here
	// insert the new value and reassign begin, which otherwise would be invaldi
	++begin;
	begin = v.insert(begin, 42); // begin insert before begin
	++begin; // advance past
}
```
This code behaviour is undefined. 
On many implementations, we'll get an infinite loop. 
The problem being that we stored the value returned by the end operation in a local variable named `end`. 
The body of the loop, we added an element. 
Adding an element invalidates the iterator stored in `end`, again, never a good idea. That iterator will NO LONGER REFER TO AN ELEMENT IN `v` NOR ANY LONGER REFERS TO ONE PAST EH LAST ELEMENT IN `v`. 
> Never cache the iterator returned from `end()` in loops that insert or delete elements in a `deque, string or vector`.

Rather than storing, let's just recompute it after every insertion: 
```
// much safer: recalculate on each trip
while(begin != v.end()){ // in the condition we are recalculating
	// do something
	++begin; // advance begin because we want to insert after this element (before the element we are up to)
	begin = v.insert(begin, 42); // returning iterator to the inserted data
	++begin;
}
```

