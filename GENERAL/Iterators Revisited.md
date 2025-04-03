#iteratorsrevisited
[[Iterators]]
In addition that what we have defined already, the library defines several additional kinds of iterators in the `iterator` header. 
These will include:
- **Insert Iterators**; These being iterators bound to a container and can be used to insert elements into the container. 
- **Stream Iterators**: These are bound to input and output streams and be used to iterator through the associated IO stream.
- **Reverse Iterators**: These iterators move backwards, rather than forward. The library containers, other than `forward_list`, have reverse iterators. 
- **Move Iterators**: These are special-purpose to move rather than copy their elements. These will be covered more and more later. 

[[Insert Iterators]]
