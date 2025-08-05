Think about something called the search space. 

[GeeksForGeeks](https://www.geeksforgeeks.org/dsa/prune-and-search-a-complexity-analysis-overview)
[Wiki Prune and Search](https://en.wikipedia.org/wiki/Prune_and_search)

**Search Space** : the set of all possible points of an optimization problem that satisfy the problem's targets or goals. 

The optimization of the domain of the function - which is essentially the same thing. 

Method in solving optimization problems, suggest by **Nimrod Megiddo** in 1983, what a legend. 

A recursive procedure in which at each step, the input size is reduced, "pruned", by a constant factor. 

Given the size of the search space, the pruning is `0 < p < 1`, where `p = 0.5` is half of the search space pruned.

**Decrease and Conquer Algorithm** - where each step we decrease by a constant factor. 

Let `n` be the input size `T(n)` be the time complexity of the whole prune-and-search algorithm. 
`S(n)` be the time complexity of the pruning step. 

We're given: `T(n) = S(n) + T(n(1 - p))`

