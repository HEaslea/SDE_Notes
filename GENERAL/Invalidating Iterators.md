Resizing, deleting or adding elements in a container, might invalidate pointers, references or iterators. 

Using an invalidated pointer, ref, or iterator is a serious programming error, much like subscripting an OOF iterator for a container. 

Here you kind of have to know a few things about the container and how memory works with them: 

- Iterators, pointers, and references to a `vector` or `string` are invalid if the container was reallocated. 
- iterators, pointer, and refs to a `deque` are invalid if we add elements anywhere but at the front or back. Adding at the front or back, iterators are invalidated, but refs and pointers to existing elements are not. 
- Iters, pointers, and refs to a `list` and `forward_list` remain valid. 

- all other iters, pointer, and refs to a `list` or `forward_list` remain valid. 
- All other iterators, refs, and pointers to a `deque` are invalidated if the removed elements are removed anywhere but the front and the back (so invalid if we add or remove anywhere but front and back). If we remove elements at the back, then the off-the-end iterator is invalid, but other iters are fine, same with the front. 
- All other iterators, refs, and pointers to a `vector` or `string` remain valid for elements before the removal point. 

It's a serious run-time error, if we use an iterator, ref, or pointer that has been invalidated. 

#managingiterators
When using iterators, it is a good idea to minimize the part of the program during which an iterator must stay valid, keep it clean. 

Code that adds or removes elements to a container can invalidate, even if you don't think so. 
This advice is super important for `vector, string and deque`. 