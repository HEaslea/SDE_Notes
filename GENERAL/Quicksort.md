[Quicksort Visualization](https://www.hackerearth.com/practice/algorithms/sorting/quick-sort/visualize/)

> 1. Select the Pivot
> 2. Move it to the end
> 3. "Partition the subarray " - place an iterator at the beginning and the end of the remaining subarray
> 4. Move the left bound until it reaches a value greater than or equal to the pivot
> 5. Move the right bound to the left until it crosses the left bound or finds a value less than the pivot
> 6. Swap the two boundaries with each other
> 7. Then move the left bound until it reaches a value greater than the pivot
> 8. Again, move the right bound until it reaches a value less than the pivot
> 9. Swap again
> 10. Continue this until the bounds cross each other : this means that everything left of the meeting is < than the pivot, therefore we just swap wherever the crossing is with the pivot. 
> 11. Then take everything to the left of the last pivot, then subarray that and do the same thing.


1. ****Choose a Pivot:**** Select an element from the array as the pivot. The choice of pivot can vary (e.g., first element, last element, random element, or median).
2. ****Partition the Array:**** Rearrange the array around the pivot. After partitioning, all elements smaller than the pivot will be on its left, and all elements greater than the pivot will be on its right. The pivot is then in its correct position, and we obtain the index of the pivot.
3. ****Recursively Call:**** Recursively apply the same process to the two partitioned sub-arrays (left and right of the pivot).
4. ****Base Case:**** The recursion stops when there is only one element left in the sub-array, as a single element is already sorted.

The main thing with this is the divide and conquer strategy. 

![[Pasted image 20241019170712.png]]

There will be a helper function, the `partition()`  function. 
There are algorithms that we partition with as well: 
1. Naive partition : here we create a copy of the array. First all smaller elements then all greater. Then we copy the temporary back to the original array. 
2. Lomuto partition: Keeping track of index of smaller elements and keep swapping. This is probably the most simple. 
3. Hoare's Partition: Fastest, we traverse array from both ides and keep swapping greater element on left with the smaller element on right while the array is not partitioned. 


```
#include <iostream>
#include <vector>

// Function to partition the array
int partition(std::vector<int>& arr, int low, int high) {
    int pivot = arr[high]; // Choose the last element as the pivot
    int i = low - 1; // Pointer for the smaller element

    for (int j = low; j < high; j++) {
        // If the current element is smaller than or equal to the pivot
        if (arr[j] <= pivot) {
            i++; // Increment index of smaller element
            std::swap(arr[i], arr[j]); // Swap the elements
        }
    }
    std::swap(arr[i + 1], arr[high]); // Place the pivot in the correct position
    return i + 1; // Return the partitioning index
}

// Function to implement Quicksort
void quicksort(std::vector<int>& arr, int low, int high) {
    if (low < high) {
        // Partition the array and get the pivot index
        int pi = partition(arr, low, high);

        // Recursively sort the elements before and after partition
        quicksort(arr, low, pi - 1);
        quicksort(arr, pi + 1, high);
    }
}
```

Primary Read Through: 
Pass reference to quicksort, denoting range of sorting (`low, high`). 
While `low < high`, we `partition()` with our low high. 
Partition then pivots around `arr[high]` with that value. 
then we take I (index) to be low - 1; which might be -1 if low is 0, for beginning of array. 
then we loop until (I think) the j(low) and high do not touch. Here j is one higher than i. 
J is moving up the array, i will move up if `arr[j]` is <= `pivot`, and then we swap `arr[i]` and `arr[j]`.
Meaning if `arr[j]` > pivot, then i doesn't move. 
i will stay on the value that is before the value greater than the pivot. 

Then the just swapped value is the one that we return, so i + 1; 
`quicksort` takes that and recursively calls for the lower end, low and i - 1, and then quicksort the upper end as well. 

What I'm gathering is going on here: 
![[Pasted image 20241019174229.png]]
This is after the first partition I believe. 
We take the pivot to be the middle, we shuffle everything around so that the numbers that are less than are on the left, and greater than are on the right. Then we have to sort those subarrays as well. The same thing happens. 

We go straight to partition, where we have the whole array on the first pass

[[Introsort]]