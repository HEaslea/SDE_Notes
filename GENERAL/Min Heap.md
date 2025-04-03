Satisfying the heap property: 
The parent is less than the children in value. 

Smallest element will always be at the root of the tree. 

The parent of the node at index i can be found at index `(i - 1) // 2`; Remembering that the `//` will round **down** at the end there, meaning that if we have (4 - 1) // 2, we will get 1: 
![[Pasted image 20240809234844.png]]

The AI book is not quite right here I don't think, in that the children nodes, in a 0 indexed array, can be found at 
1. 2 * i + 1  
2.  2 * i + 2

![[Pasted image 20240809235033.png]]
Yeah the AI book is wrong here I believe. 

Building the array is not really  necessary here, however, if we did have a tree that we wanted to traverse, then we could look at this, we essentially go through the layers from left to right: 
Of course starting at the root node, then we move down a layer, and start at the left node, then the right, then the next layer, which we go through left to right, so the left of the left, then right of the left, then left of the right and then right of the right etc. etc.


There are some operations that we should have when we implement this 
```
getMin(); // which is O(n)

extractMin(); // extracting the root, however, we then need to heapify() in order to get the heap property remained, which is time of O(log n). 

insert(); // inserting will take O(log n). 
// We add at the end of the tree, if it is smaller than the parent node, then we need to heapify again. 

delete(); // 
```


Some C++ implementation:
```
#include <iostream> 
#include <climits> 

using namespace std;

void swap(int *x, int* y);

class MinHeap
{ 
	int * harr; // pointer to an array of elements in the heap
	int capacity; // max possible size of min heap
	int heap_size; // current number of elements in min heap

public: 

	MinHeap(int capacity);

	// to heapify a subtree wiht the root at the given index 
	void MinHeapify(int i);


	intparent(int i){ return (i - 1)// 2;}

	int left(int i ) { return 2 * i + 1;}

	int right(int i ) { return 2 * i + 2;}

	int extractMin();

	void deleteKey(int i);

	void insertKey(int k);
};
```

```
MinHeap::MinHeap(int cap)
{ 
	heap_size = 0; 
	capacity = cap;
	harr = new int[cap];
}
```

```
void MinHeap::insertKey(int k)
{ 
	if(heap_size == capacity) // err out
		return;

// super simple add and increment the size 
	heap_size++;
	int i = heap_size - 1;
	harr[i] = k;


	// fix the min heap property if it were violated here
	while(i != 0 && harr[parent(i)] > harr[i])
	{ 
		swap(&harr[i], &harr[parent(i)]);
		// don't need to worry about the other node of course
		i = parent(i);
	}
}
```

```
void MinHeap::decreaseKey(int i, int new_val)
{ 
	harr[i] = new_val;
	while(i != 0 && harr[parent(i)] > harr[i])
	{ 
		swap(&harr[i], &harr[parent(i)]);
		i = parent(i);
	}
}
```

```
// a recursive method to heapify a subtree with the root at the given index
// 
void MinHeap::MinHeapify(int i)
{ 
	 // startin from node i
	int l = left(i);
	int r = right(i);
	// check if that position does exist for both
	// remember that the parent node should be the smallest
	if(l < heap_size && harr[l] < harr[i])
	{ 
		smallest = l;
	}
	if(r < heap_size && harr[r] < harr[smallest])
	{ 
		smallest = r;
	}

	if(smallest != i)
	{ 
		swap(&harr[i], &harr[smallest])
		MinHeapify(smallest);
	}
}
```
Swap is quite a big deal here. 

```
void swap(int* x, int* y)
{ 
	int temp = *x;
	*x = *y;
	*y = temp;
}
```

I think the best way to think about it, is to think of the array and positions, essentially we are looking at three positions, and if the ones on the right are not larger than the one on the left, then we have to swap, however, we will only have to swap the one of course, the smallest of the three. 

```
// Sorting something in C++ 
void kLargest(int arr[], int n, int k)
{ 
	// sort with the largest elements first
	std::sort(arr, arr + n, greater<int>());

	for(int i = 0; i < k; ++i)
	{ 
		cout << arr[i] << " ";
	}
}
```

```
// a cool way with binary search
int findKthLargest(int arr[], int n, int k)
{ 
	int low = INT_MAX; int high = INT_MIN;

	for(int i = 0; i < n; i++)
	{ 
		low = min(low, arr[i]);
		high = max(high, arr[i]);
	}

	// here's the binary search
	while(low <= high)
	{ 
		int mid = low + (high - low) / 2;
		int count = 0; 
		// count every element that is greater than mid
		for(int i = 0; i < n; i++)
		{ 
			if(arr[i] > mid)
				count++;
		}	

		// if there are at least k elements that are greater than mid
		// search the right half
		if(count >= k)
			low = mid + 1
		else 
			high = mid - 1;
	}
	return high;
}
```


#### Returning K Largest Elements 
Using a min heap, which seems odd, however, we are going to swap the root of the min heap with elements that are larger than in, meaning that we are building the k largest elements. 
This has to be how it is. 
If it is a max-heap then we are not going to get the largest elements, we are just gonna keep swapping the largest one. This won't work. 

Imagine that we have a heap interface and implementation already done: 
```
def k_largest_elements(arr, k): 
	heap = Heap(element_priority=lambda x: -x[1])
	for i in range(len(arr)): 
		if len(heap) >= k:  # if we have a k large min heap
			if heap.peek()[1] < arr[i]:
				heap.top()
				heap.insert((i, arr[i])) # inserting the tuple
		else: # if there are more elements to add to make k
			heap.insert((i, arr[i])) # just insert if there are not k elements in there
	return [heap.top() for _ in range(k)]
```

