[Visualization](https://www.cs.usfca.edu/~galles/visualization/HeapSort.html)

Not to be confused with Mergesort [[Mergesort]]. 

Heap sort is the idea that we make a heap, and then we extract from that heap in order to get a sorted array. 

Remember that we can use an array, if the tree that we are producing is complete, which it always will be, due to our definition. 

Meaning that we can go from child to parent very easily: 
```
iLeftChild(i) = 2 * i + 1  (or + 0 depending on which implementation I've seen)
iRightChild(i) = 2 * i + 2 (or + 1 again, depending)
iParent(i) = floor((i -1) / 2) (again, I think this changes)
```

You would do well to remember and have a look at Binary Heap Implementation here. 

First Phase : Building the heap out of the data

Second Phase : We take the root (as either the largest or smallest depending on). 

Say that the first phase begins by rearranging the array into a binary max-heap. 
Then we just swap the root with it's last element, this means that it's part of the sorted suffix. This means that the largest (or smallest depending on implementation again) will be at the end. 

**Sorted Suffix**

**Binary Heap Prefix**

Every single step shrinks the prefix and grows the suffix. 

This means that we have damaged the binary heap, as we have just put a node at the root that won't work, therefore we need to `siftDown(). 

`heapify()` at the beginning to the make the max heap, is going to be in `O(n)`, as we have to go through every element once. 

Then the `siftDown()` could be called `n` times, and each time we require `O(log n)`, as we have to traverse from the root node every time, and the height of a complete binary tree is going to be `log_2(n)`. 

[Reference](https://en.wikipedia.org/wiki/Heapsort)

Essentially the rest of it can be learnt from [[MinHeap and MaxHeap]] 
