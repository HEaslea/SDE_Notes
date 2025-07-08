[Cpp Ref](https://en.cppreference.com/w/cpp/container/deque)

```
template <class T, class Allocator = std::allocator<T>>
class deque;
```

Indexed sequence container allowing for fast insertion and deletion at both **ENDS**. 

They are not store contiguously. Typically using a sequence of individually allocated fixed size arrays, additional bookkeeping, meaning indexed access to deque must perform two pointer dereferences, compared to vector that does one. 
Expanding is cheap, no need for reallocation. 
usually large minimal memory cost : Holding just one element has to allocate its full internal array. 

Random access - we love it `O(1)`. 
Insertion or Removing - At beginning or end - constant `O(1)`
However, removing not at the beginning or end is `O(n)`. 
This is because all the elements have to be shifted if we do either. 
There may be some cache misses, due to the fact that most fixed sized arrays might not be in the same area. 

### Fit Me in the Cache
The array size that they used are usually small enough to be able to fit inside the array. 
And they are contiguous, meaning that they do really well when we want them to come into the cache. 
Holy fuck this is beautiful, actually fucking beautiful. 

However, they writing of it, is apparently not as efficient, however, you would have to test it as per usual. 

A list has great insertion time, as you just change the pointers, however, adding in an array, in a particular point, well, that's rough, everything will have to be shifted, and then if we have to reallocate all that shit, then we run into a problem. 

### Top Level `std::map`
It's not an array of pointers, it's an `std::map`, often called just a **map** or a **blocking table**. 
Each bucket here, points to a fixed-size memory block, holding the actual elements themselves. 

### Second Level : similar to `std::array<Element>`
Elements are contiguous within a block, however, the blocks themselves are not contiguous, they are separated, pointed to the by the top level, the `std::map`. 

The idea being: 

`push_back` - Amortized `O(1)` - Add element to the last block, or allocate a new one if full. 

`push_front`- Amortized `O(1)` - Same as above, but at the front. 

`pop_back` - Amortized `O(1)` - Remove last element, deallocate block if empty. 

`pop_front` - Amortized `O(1)` - Same as above but at the front. 

`operator[]` `O(1)` -  Map index to block, then offset

Insert/Erase in middle `O(n)` - requires shifting elements, similar to a vector. 

### `push_front` 
`push_back` makes sense that it is amortized `O(1)`. 
`push_front` doesn't make that much sense. 
The way it seems is that we have the `std::map`, and we have the "vectors" at each pointer, and the front would need to move everything else in order to get through. 

The idea that we take with us, is that the `std::map` has extra space at both the front and the back, sort of like a buffer. 

When we do `push_front()`, we look to see if there is space at the front, if not, then we need to allocator before the current first block, and then we would add a pointer to that block in the map. 

### Never Assume Implementation
Although one might assume that a `deque` is just a large allocated chunk of memory, and we have two pointers, and then reallocate when used up. That's not the case. 

Therefore, never assume the way things are implemented. 

