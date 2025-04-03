### Ways of Implementing

## Binary Heap (Min Heap or Max Heap)
Complete binary tree.  - allowing for array implementation. 
Operations: 
- Insert `O (log n)` - based on the height of the tree
- Remove min/max : `O (log n)` as we have to bubble down. 
- Peek `O (1)`
The idea is that we are spreading the load over all the functions of implementations. 

Common, simple and efficient `priority-queue`, in C++ is implemented using a binary heap. 
Space: `O (n)`
The idea being we are bubbling down and bubbling up. 


## D-ary Heap (Ternary, Quaternary)
Each node can have d children instead of just 2. 
Implementation from binary heap is only slightly different: 
- Left = `d * i + 1`
- right = `d * i + 2`  where d is number of children

- Insert: `O (log _d n)`
- Remove min/max : `O (d log_d n)`
- Peek : `O(1)`
Useful to decrease number of comparisons (if  `d > 2`), preferred in Djikstra's Alg. 
Space:  `O (n)`


## Sorted Array
Just keeping an array sorted. 
- Insertion: `O(n)` not great for insertion heavy workload
- Remove min/max : `O(1)` (just pop the first/ last element)
- Peed: `O(n)`
This would be great if we just had to sort once, then we can just subtract `n` minimal. 
If there are lots of inserts, then this is going to be a little troublesome. 

Space: `O (n)` still fine, as the others are array based as well. 
Removal is immediate, no `O (log_d n)`

## Unsorted Array or Linked List

No maintained order: 
- Insert : `O(1)` just put at the front or end
- Remove min/max `O (n)` as we have to scan through
- Peek `O(n)` this is a massive issue here as well
When insertions are frequent, but weirdly min and max is not. 

Space : `O(n)`
Inserting is super fast, removing the min/max requires linear search. 

## Fibonacci Heap 
A collection of heap-ordered trees. 

- Insert: `O(1)`
- Remove min/max : `O(log n)` amortized
- Decrease Key: `O(1)` amortized
- Peek: `O(1)`
Super good for Djikstra's
`O(n)`
Very complex to implement, but optimal for certain graph algorithms. 

## Pairing Heap 
A multi-way heap that combines simplicity with efficient operations. 
- Insert `O(1)`
- Remove min/max : `O (log n)` amortized
- Decrease Key: `O(log n)`
- Peek `O(1)`
Simpler than fibonacci. Simpler Alternative. 

## Skew Heap
Binary heap variant that allows skewed structure for simplification. 
- Insert : `O(log n)`
- Remove min/max : `O (log n)`
- Peek : `O (1)`

Heap structure without worrying about balancing. 
Space `O(n)` they all appear to be that. 