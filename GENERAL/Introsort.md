In relation to sorting in `std::vector`, within code such as this: `std::sort(std::begin(numbers), std::end(numbers))`. 

Not just any sorting alg, it's a sort of hybrid of a bunch of different algs: quicksort, heapsort, and insertion sort.

This means that the sort can adapt given some traits about the `std::vector`. 

Starting with Quicksort [[Quicksort]], it will go until a certain limit is given, then it will assume a bad pivot (I think this is the smallest number). 
Then it will move onto Heapsort  (which I need to write about), which guarantee `O(n log n)` - for worst case behaviour. 
If the vector is small (around n < 16 is a common limit) - it's faster on tiny array dues to the low overhead of course. 
Really there is nothing crazy going on here, it's just that we pick and choose which algorithm we pick. 

**This means that we never degrade to `O(n^2)`**. 

