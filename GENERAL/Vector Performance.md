Random Access - using `O(1)` in order to access
Cache Locality - Spacial locality - fewer cache misses. 
Insertions at the end  - Limitations - however adding to the end is `O(1)`
Insertions not at the end -  in themiddle - may have to shift all the other elements - however, this still might out perform something like a `std::list` due to locality.

Deletions - `O(n)` if not at the end, everything has to move to accomadate. 

Comparison: 
`std::list`  - `O(n)` for access. 
`std::deque` - efficient insertions of both front and back - however, access is still slow - more cache misses. 
`std::array` - static array with fixed size.

Matching our problems to our data structure. 

Resizable - dynamic array 
Good cache locality - great pick for this
Graphics and Game Development - resizing due to various numbers of things. 

Data structures that are optimized for serialization and deserialization. 

In distributed systems, data might be better represented in structures optimized for serialization and deserialization, even if within a single node, `std::vector` might seem the best choice.

`serialization` - in-memory structure into a format suitable for transmission. 
`deserialization` - from transmission to in-memory 
`std::vector` not optimized for this as vector holds a pointer to a separate heap allocation, size and capacity. 

`std::vector` holding non-contiguous memory relative to its meta data - in that pointer to the memory in heap. 
Pointers are not meaningful across nodes - different areas in a distributed system. 

There are better alternatives for distributed systems: 
`FlatBuffers`-
`Protocol Buffers` - `Cap'n Proto` - `FlatBuffers`
They are contiguous, portable and self-describing manner, easier to deserialize and serialize. 

Alternatives - `std::deque, list, set`
`vector` is a safe bet if you don't know what to start with. 

