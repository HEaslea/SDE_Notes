#### Lists
`list` being a dynamic array, in that we can add elements to it, similar, but not exactly to `std::vector` in C++. 

The way that this is implemented as an array of pointers to `PyObject`, not the objects themselves. 

Say that we have `l = [1, 2, 3]`
`l` being a structure that contains: 
- A pointer to a block of memory ( an array of pointers to integers 1, 2, 3 ). 
- A capacity (how many items it can hold before it resizes). 
- A size, the number of items currently, actually in the list. 
This is the exact same as the `std::vector`. 

The issue obviously, then, being that when we resize, we have to reallocate all the memory. 
The difference being, crucially, that Python will hold the pointers to the objects `PyObjects`, and `std::vector` can (without explicitly holding pointers) will hold the objects themselves. 
In C++, we have to be aware that this is going to be a big process, copying all that data over when we reallocate. 
Python `list` only store pointers to the object themselves, this level of indirection is great when it comes to a "shallow copy" of that data, however, when accessing an object, that level of redirection can be a little slow. But not in 99% of basic cases. 

The key is remembering that lists are very very similar to a `std::vector<*PyObject>`. 

Remember that deleting in lists is going to be an issue, `O(n)`, as we have to move everything around. 




### Dicts
Under the hood, just a hash table `{key : value}`. 

- A sparse array of buckets (initially, only about 8). 
- Each bucket contains: 
	- A hash of the key that is used
	- The key and the value

They use open addressing with probing, no chaining. 

Remember that open addressing is that everything is in the hash table array itself. 
Closed is where we use chaining. 
Python uses open addressing, in that we must probe in order to get to the right `key : value`.

`std::unordered_map` however, uses closed addressing. 

If we have: 
`d = {"a" : 1, "b" : 2}`

This means that the hash of `a` -> finds slot, stores key and value. 

Adding is very simple: 
`d["c"] = 3`

`hash("c")` , then will modulo with the capacity (number of buckets) in order to find a slot. 
If that one is taken, then try the next one, when load factor is ~2/3, it will resize. 

Resizing is really just doubling. 

The issue is that after we resize, we have to rehash all the keys, otherwise the new hashing method, that uses modulo based on the size will fail. 
