
### Ordered#
`std::map`, `std::set`, `std::multimap`, `std::multiset` - these last tow allow the same key. 

Due to having to be ordered, they utilise **Balanced Binary Search Tree** (usually Red-Black Tree).
Due to this, most of the `O` time is `O(log n)`. 
These trees are self balancing, therefore we are never going to get `O(n)`. 

### Unordered
`std::unordered_map`, `std::unordered_set`. 
These utilise hash maps. 
Therefore most of the time complexity `O(1)`. 
If there is really bad hash collision, then there might be `O(n)`. 

![[Pasted image 20250710194432.png]]

### Open and Closed Addressing
**Open**
All elements stored in the hash table itself.
On collision, they are stored in the next empty slot (linear or quadratic probing). 

**Closed**: 
Each table slot hold a list (or bucket). 
On collision, we move through that list and store. 

[Unordered Map](https://en.cppreference.com/w/cpp/container/unordered_map.html)

