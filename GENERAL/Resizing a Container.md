#resizingacontainer

With the usual exception of arrays, that can't be resized, to make a container larger or smaller. 
If the current size is larger than that of the newly asked size, then we will lose elements. 
Elements are deleted from the back. 

If we want it to be larger, then we will add elements to the end, default elements of that type. 

```
list<int> ilist(10,42); // ten ints of 42
ilist.resize(15); // adds five elements of value 0 to the end
ilist.resize(25,-1); // adds ten elements of value -1 to the back of the list
ilist.resize(5); // erases 20 elements from the back of the list. 
```

![[Pasted image 20240317201400.png]]
Remember that if we are to add elements, then the element type will have to have a default constructor. 

#containeroperationsinvalidatingiterators

Operations that add or remove elements from a container can invalidate pointers, references, or iterator to container elements. 


### Iterators Make Algorithms Container Independent

### But Algorithms do Depend on Element-Type Operations

Although the first title is true, most of the algorithms use one (or more) operations on the element type. 
eg. using the element type's == operator to compare each element to the given value. Of course, if our own objects aren't able to do this, then we are in trouble. 
Most algorithms will give us the ability to use our own operation in place of the operation that would be used by default.

> Algorithms will never execute container operations
> The generic ones do not themselves execute container operations. They operate solely in terms of iterators and iterator operations. 
> The fact that they operate as such has a perhaps surprising but essential implication: They never change the size of the underlying container, which is key, as most of our containers will invalidate the iterators if they do (vectors will change the location and mem address).
> They may change the elements stored in the container, and they may move elements all over, however, they do not change the size, they never add or remove elements directly.
> There is a special class of iterator, the inserters, that do more than traverse the sequence to which they are bound. When assigning to these iterators, they execute insert operations on the underlying container. When an algorithm operates on one of these iterators, they *iterator* may have the effect of adding elements to the container. However, the algorithm will never do so itself.


### A First Look At Algorithms
The library provides over 100 algorithms. They all have consistent architecture, thank God. 
Understanding this architecture makes learning and using the algorithms easier than memorizing all 100+ of them. 
They mostly (close to all) will work over a range of elements. 
We'll refer to this range as the "input range". 
The first two parameters will always be this idea of a range. 
These are iterators denoting the first and one past the last elements to process. 
ONE PAST THE LAST, ONE PAST THE LAST for ALGORITHMS

The idea is read, write and rearrange 

