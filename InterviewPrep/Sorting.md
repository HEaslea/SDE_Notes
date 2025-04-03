#### Stable, Unstable Sort and In-Place Sorting
Maintain relative order of equal elements (eg. Merge Sort is stable, Quick sort is not)

Quick sort and Heap sort are in place, meaning that they don't need any extra memory, which is something that we should be looking out for. 

### Quick Sort
First we pick a pivot. 

Smaller elements than pivot are on the left, larger on the right. 
Apply the same process to the left and right subarrays. 

```
int partition(std::vector<int>& arr, int low, int high)
{ 
	int pivot = arr[high]; // choose the last element as pivot
	// we can change that pivot if we want
	int i = low - 1; // Index of the smaller element
	for(int j = low, j < high; ++j)
	{ 
		if(arr[j] <= pivot)
		{ 
			++i; 
			std::swap(arr[i], arr[j]);
		}
	}
	// swap the pivot again
	std::swap(arr[i + 1], arr[high]); // place pivot at the next i
	return i + 1; // return wher we put the pivot
}

void quicksort(std::vector<int>& arr, int low, int high)
{ 
	if(low < high)
	{ 
		int pivotIndex = partition(arr, low, high);
		// pivotIndex where we got the pivot
		quickSort(arr, low, pivotIndex - 1);
		quickSort(arr, pivotIndex + 1, high);
	}
}
```


The key part of the `Quicksort` is the `partition()` of which there can be a bunch of algorithms. 
- Pick first or last
- Pick a random element as a pivot

```
int partition(vector<int>& arr, int low, int high)
{ 
	// low and high are the indexes
	int pivot = arr[high]; // getting the pivot

	int i = low - 1; s

	// traverse arr[:low .. high] and move all the smaller elements
	// to stay on the left hand side
	
	// j moves up, while i is for the swapping point
	// hence why i is lower than low at the start, 
	// as we increment first
	for(int j = low; j <= high - 1; j++)
	{ 
		if(arr[j] < pivot)
		{ 
			i++; 
			std::swap(arr[i], arr[j]); 
		}
	}
	std::swap(arr[i + 1], arr[high]); // swap with the pivots
	return i + 1; // return that pivot point
}

// then the quicksort is just like the recursive point of this
void quicksort(vector<int>& arr, int low, int high)
{ 
	if(low < high)
	{ 
		// pi is the partition return index of pivot
		int pi = partition(arr, low, high);

		// call them for smaller subsections
		quicksort(arr, low, pi - 1);
		quicksort(arr, pi + 1, high);
	}
}
```

So the main parts: 
`partition` and the recursive part for the left of the partition, and the right, `quicksort`. 

The thing that we are finding is that for every pivot point, we are finding the Kth point that it goes, every time we are finding the right spot for the pivot. 
Every time, we are finding the right spot for the pivot. 

```
// let's partiton between these points
int partition(std::vector<int>& arr, int low, int high)
{ 
	int pivot = arr[high]; // just take the last number
	int i = low - 1;

	for(int j = low; j <= high; j++)
	{ 
		if(arr[j] <= pivot)
		{ 
			++i;
			std::swap(arr[i], arr[high]);
		}
	}
	// then get pivot into right place 
	std::swap(arr[i + 1], arr[high]);
	return i + 1;
}
```


Then we need the recursive, remembering that the pivot is in the right place: 
```
void quickSort(std::vector<int>& arr, int low, int high)
{ 
	if(low < high) // they can be ==, as sort of 1 element is allowed
	{ 
		// get the pivot point 
		int pi = parition(arr, low, high);

		// go left
		quickSort(arr, low, pi - 1);
		// go right
		quickSort(arr, pi + 1, high);
	}
}
```
Remember that high is going to be the highest index there is. 

Nailed it now. 


### Merge Sort
The old divide and conquer. 
See algorithm design book, go through that chapter again, there is quite a lot in there, then go through the ones that are not in there, that are more "basic": 

For both of these, there is a recursive function, then there is the actual function, algorithm that does the work: 
```
void mergesort(std::vector<int>& arr, int left, int right)
{ 
	// same as quick sort, similar to if low < high
	if(left < right)
	{ 
		// then here in quicksort we just have the pi = partition
		int mid = left + (right - left) / 2; 
		// find the middle point of this iteration

		// then, like quicksort as well
		// we do the left and the right
		// make sure we include the middle element this time
		// quick sort, we don't have to include the pivot
		// as quick sort puts the pivot in the right place
		mergesort(arr, left, mid); 
		mergesort(arr, mid + 1, right);
		
		// merge the sorted halves
		merg(arr, left, mid, right); 
	}
}
```

Then we just implement this `merge` function: 

```
void merge(std::vector<int>& arr, int left, int right)
{ 
	int n1 = mid - left + 1; // size of the left subarray
	int n2 = right - mid; // size of the right

	std::vector<int> L(n1), R(n2);

	
	// copy the data into the new arrays
	for(int i = 0; i < n1; i++)
		L[i] = arr[left + 1];
		
	for(int j = 0; j < n2; j++)
		R[j] = arr[mid + 1 + j];


	int i = 0, j = 0, k = left; // k is the to put these numbers
	while( i < n1 && j < n2) // while both are < range
	{ 
		if(L[i] <= R[j])
		{ 
			arr[k] = L[i];
			i++;
		} else 
		{ 
			arr[k] = R[j]; 
			j++;
		}
		k++; // go to next the point in the actual array
	}

	// copy the remaining elements of L[] (meaning R[] is empty)
	while(i < n1)
	{ 
		arr[k++] = L[i++];
	}
	
	// the same for right (meaning L is empty)
	while(j < n2)
	{ 
		arr[k++] = R[j++];
	}
}
```


