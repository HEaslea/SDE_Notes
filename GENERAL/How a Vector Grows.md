#growingavector
To support fast random access, `vectors` are stored in contiguously - each element is adjacent to the previous element. 
Part of the implementation leaks into the interface here. Meaning that how it works has to leak into how it is used. Think of it like that. 
Remember that interface (think of the air fryer) would be the bridge between the user and its implementation. The air fryer control panel gives us the ends to the means. That we can use something that we have no idea how it works. 


If there is no room for the new element, the container can't just add a new element, somewhere else in memory, otherwise it would not be contiguous and we would not have fast random access. 
The container must allocate new memory to hold the existing elements PLUS the new one, move the elements from the old to the new. 
And then deallocate the old memory. 
However, if we did this every time that we wanted to allocate, the performance would be so slow. 
To avoid this, we use allocation strategies to reduce the number of times the container is reallocated. 
When they have to get new memory, the allocation will usually allocate capacity beyond what is immediately needed. 
The container holds this storage in reserve and uses it to allocate new elements as they are added. 
Therefore capacity will be larger than current size. 
It's really efficient actually, meaning that it is faster than most other containers. 

### Members for Managing Capacity
#capacitymanagement
`capacity()` will tell us how much memory that we can hold. 
The `reserve()` lets us tell the container how many elements it should be prepared to hold. 
![[Pasted image 20240318005935.png]]
They don't change the number of elements in the container, just the memory allocation for it. 

If the asked for size in `c.reserve(n)`, as in if n < already pre-allocated memory , then reserve will do nothing. It won't even cause it to shrink, it will just. 
Similarly, `resize()` will not do anything with memory. 

### `capacity` and `size`
`size` is the number of elements the container already holds. 
`capacity` is how many elements it can hold before more space must be allocated. 

```
vector<int> ivec;
// size should be zero, capacity is implementation defined
cout << "ivec.size " << ivec.size()
	<< "   capacity: " << ivec.capacity() << endl;
// give ivec 24 elements
for(vector<int>::size_type ix = 0; ix != 24; ++ix){ 
	ivec.push_back(ix);
}
// size should be 24: capacity will be >=24 and implementation defined
```

![[Pasted image 20240318010722.png]]
```
ivec.reserve(50); // sets capacity to at least 50
```
Then we might want to use that extra capacity: 
```
// add elements until we have used up that empty space
while(ivec.size() != ivec.capacity())
	ivec.push_back(0);
// capacity should be unchanged, as we haven't pushed that threshold
```

I wrote a cool little thing: 
```
void TestVect(vector<string>& nv){ 
	string word;
	while(cin >> word){ 
		if(word == "DONE") break;
		nv.push_back(word);
		cout << nv.size() << "  " << nv.capacity() << endl;
	}
	for(string s : nv){ 
		cout << s << "   ";
	}
	cout << endl;
}
```
Really simple

We could even get an idea of how the implementation strategy works for vectors. 

Remember that `shrink_to_fit` is only a request, like inline, it means that there is no guarantee that the library will return the memory.
