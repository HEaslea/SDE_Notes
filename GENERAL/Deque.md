[Cpp Ref](https://en.cppreference.com/w/cpp/container/deque)

```
template <class T, class Allocator = std::allocator<T>
class deque;
```

Indexed sequence container allowing for fast insertion and deletion at both **ENDS**. 

They are not store contiguously. Typically using a sequence of individually allocated fixed size arrays, additional bookkeeping, meaning indexed access to deque must perform two pointer dereferences, compared to vector that does one. 
Expanding is cheap, no need for reallocation. 
usually large minimal memory cost : Holding just one element has to allocate its full internal array. 

Random access - we fucking love it `O(1)`. 
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


#### I Fucking Hate and Love Strings
Strings do what they do really well, however, if we have a really really really large string that is larger than 32KB, then we are going to struggle to get all of that into L1 cache, we aren't gonna struggle, we are just going to 