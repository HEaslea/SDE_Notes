### Max Heaps
Binary Tree Structure:
- Complete Binary Tree : All levels are fully filled except the last, which is full from left to right. 
- Full Binary Tree: Every node has either 0 or 2 children (no nodes with only one child). 

The main thing is that the value of a node is greater than or equal to that of its children, which is known as the max heap order property, this property must be maintained in order to retained the idea of a max heap. 
They are often implemented using arrays. 
For a node at index `i`, its left child is at index `2 * i  + 1`, it's right being at `2 * i + 2`. 
The idea being that using an array, we are always inserting into the very last level that isn't complete. 

Then we would do some swaps in order to maintain that max-heap-property. 

#### Advantages: 
- Efficient Insertions and Deletions: Both insertion and deletion operations have a time complexity `O(log n)`, making them efficient. 
- Easy Access to Maximum Elements: `O(1)`, very easy to see as well. 


#### Disadvantages: 
- Inefficient Searching: Not allowing for easy search for a specific element. In worst case we will have to visit all node. 
- Not a stable data structure: Relative order of equal elements may not be preserved. 

When we use an array in order to do this, the swapping is actually really easy. 
Doing it with structs etc. is going to be a bit more of a challenge. 

`heapify` is a word that comes up a lot, and its a word to describe the idea that we satisfy the property of the heap, either a max heap or min heap. 
Due to the binary tree thing, this is usually recursive as is DFS of a tree.

```
// i is the current index we are on
void heapify(int arr[], int n, int i) // usually recursive
{ 
	int parent = (i - 1) / 2; // index of parent of this node
	if(parent >= 0)
	{ 
		if(arr[i]) > arr[parent]
		{ 
			swap(arr[i], arr[parent]);
			heapify(arr, n, parent);
		}
	}
}

void insertNode(int arr[], int& n, int Key)
{ 
	n = n + 1; // increase the size of the heap by 1
	
	arr[n - 1] = Key;

	heapify(arr, n, n - 1);
}

void deleteRoot(int arr[], int& n)
{ 
	int lastEle = arr[n - 1];
	
	arr[0] = lastEle;

	n = n - 1;
	heapify(arr, n, 0);
}
```

The idea with `deleteRoot` is that we delete the root node, then use the last node in its place, then we just `heapify` that structure. 

The key behind these structures, is that they are completed, meaning that we can use an array, we know that the last index will be the last node. 
Take a complete quad tree, this means that we have four nodes for every parent if completed. 
This just means that our implementation is different, if they are not completed, then we might have to use nodes as the structures are more complicated. 


Here's something that is really cool is that `heapifying` an array is faster than actually just sorting it. Therefore, whenever you need the max of an array, let's just `heapify` it. 

Finding the largest element once, might be faster with the linear thing, finding multiple times and then having to dynamically add and remove things at various points, yeah, then `heapify`, try and see the difference there, it's subtle. 

###### `std::max_element`
```
template <class ForwardIt> 
ForwardIt max_element(ForwardIt first, ForwardIt last)
{ 
	if(first == last)
		return last;
	ForwardIt largest = first;

	while(++first != last)
	{ 
		if(*largest < *first)
			largest = first;
	}
	return largest;
}
```

### Inserting From Book: 
```
void max_heap_insert(heap_element heap[], int *num_heap, int receipt_index, int cost)
{ 
	int i; 
	heap_element temp;
	(*num_heap)++;

	heap[*num_heap] = (heap_element){receipt_index, cost};
	
	i = *num_heap;

	while(i > 1 && heap[i].cost > heap[i / 2].cost) // while this cost is greater than the parents cost
	{ 
		temp = heap[i];
		heap[i] = heap[i / 2];
		i = i / 2;
	}
}
```

#### Heap Sort

```
void heapify(vector<int>& arr, int size, int i)
{
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;

    // checking which is largest
    if(left < size && arr[left] > arr[largest])
        largest = left;

    if(right < size && arr[right] > arr[largest])
        largest = right;

    if(largest != i)
    {
        std::swap(arr[largest], arr[i]);
        heapify(arr, size, largest);
    }
}

void heapSort(std::vector<int>& arr)
{
    int n = arr.size();  // build the heap
    for(int i = n / 2 - 1; i >= 0; --i)
    {
        heapify(arr, n, i);
    }
	
    for(int i = n - 1; i > 0; --i) // extract from the heap
    {
        std::swap(arr[0], arr[i]); // move current root to the end vice versa
        heapify(arr, i, 0); // using reduced heap size
    }
}

int main()
{
    std::vector<int> arr = { 12, 11, 13, 5, 6, 7};
    heapSort(arr);
    printoutCont(arr);
```

The great thing about the heap sort is that we are arranging in place. 
Similar to the partition sort: 

##### Building the Max Heap
Remember that we are creating a binary tree, that we can do in the array. 
Starting from the first non leaf node, (n / 2 - 1) and then call `heapify` for each node up to the root. 
This ensures that the largest is always at the top of the heap. 
I believe this goes through every parent node and checks that we are under the max heap property. 

##### Extracting the Maximum Element
Swap the root with the last element in the array. 
Reduce the heap size by one (ignore the last element - it's now sorted); 
Call `heapify` on the root to restore the heap property for the reduced heap. 

#### Heapify
Recursively adjusts a subtree to maintain the **max heap property**: 
- Compare the parent with its left and right children
- Swap with the largest child, if needed. 
- Repeat until the heap property is restored. 
Starting at a node, compare with the children. 
If a child violates the max heap property (it's larger, or smaller than the parent), `heapify` will swap. 
Then recursively calls itself, on the affected child node, meaning the one that we swapped. 
That means that we go deeper, only for the node that is affected. 
If we have no swap, then we can just break out. 
##### Complexity
**Time**: 
Building the heap `O(n)`
Sorting (extracting and `heapifying`) `O(n log n)`
**Space**:  `O(1)` in place sorting, no extra memory. 




##### Counting Sort
This has nothing to do with trees or heaps or anything, I just like this idea a lot: 
```
void countSort(vector<int>& arr, int range) // range being the highest number we will count
{ 
	std::vector<int> count(range);
	std::vector<int> output(arr.size());

	for(int num : arr) // increment at index num for every num in arr
		count[i]++;

	for(int i = 1; i <= range; ++i) // cumulatively add them
		count[i] += count[i - 1];
	// meaning that count is now cumulative


	// building the sorted array
	for(int i = arr.size() - 1; i>=0; i--)
	{ 
		output[count[arr[i]] - 1] = arr[i];
		count[arr[i]]--;
	}
}
```
Iterate through the original array: using the count array to determine the correct position for that element in the sorted array. 
Place that element in the sorted array. 
Decrease the count for that element to correctly place any subsequence occurrences. 
This is great because what do we notice, that there are no comparative operators, this means that if we have a value of a struct, we don't need the extra `< >` operators. 

## Segment Trees
## Treaps
#treaps
Is a mixture of binary search trees and heaps. 
Very close to the random binary search tree. 
They maintain a dynamic set of ordered keys and allow binary searches among the keys. 
Each node in the treap is assigned a priority (a random number, usually chosen uniformly). 
We maintain a max heap property based on priorities: 
The priority of a parent node is > or = to that of the priorities of the children. 
![[Pasted image 20241206000152.png]]

A good way to remember them here: 
- Pre-Order: meet on the left
- In-Order: meet on the bottom
- Post-Order: meet on the right

The root node is the maximum priority node. 
Searching is super easy and quick. The heap portion means that our trees will be complete. 
The Nodes in a treap are: 
1. Data/key field
2. priority field

Continuing with Treaps: 

```
struct Node 
{ 
	int key; 
	int priority;
	Node* left;
	Node* right;

	Node(int k) : key(k), priorty(rand()), left(nullptr), right(nullptr) {}
};
```


### Segment Trees Themselves
Used for efficient range queries, and updates on an array. 
Each node will represent a segment, a range of the array. 

The root node represents the entire array. 
Internal nodes represents a subrange of the array. 
The leaves will represent a single element in the array. 
This one shows the sum of these.
![[Pasted image 20241206122003.png]]

- Good for finding range sum queries
- Searching index with given prefix sum
- Finding range Max/Min
- Counting frequency of range max/min
- Finding range GCD/LCM
- Finding range AND/OR/XOR 
Notice the common theme, finding a range of some sort. 
Any range queries are going to be great for this sort of thing. 

Build time: `O(n)`
Query Time : `O(log n)`
Update Time: `O(log n)`

Remember that leaf nodes will be the individual elements: 

Say we are given the array `A = [1, 3, 5, 7, 9, 11]`
Given the query, find the sum of the elements between indices `l` and `r`. 

They are usually represented as an array. 
For input size `n`, the segment tree can be sorted in size up to 4n. 

```
class SegmentTree
{ 
	vector<int> tree; // the actual tree
	vector<int> arr; // input
	int n;  // size of tree
	
	void build(int node, int start, int end)
	{ 
		if(start == end)
		{ 
			tree[node] = arr[start];
		} else 
		{ 
			int mid = (start + end) /2 ;
			int leftChild = 2 * node + 1;
			int rightChilde = 2 * node + 2;

			// recursive building
			build(leftChild, start, mid);
			build(rightChild, mid + 1, end);
			// recurse first
			// then add up the answers to that

			// tree should be built by now, therefore we casn access those elements
			tree[node] = tree[leftChild] + tree[rightChild];
		}
	}

public: 
	SegmentTree(const vector<int>& inputArr)
	{ 
		arr = inputArr;
		n = arr.size(); 
		tree.resize( 4 * n ); // make actual tree much larger 
		// resize so that we can []
	
		build(0, 0, n - 1); // start at 0, and n - 1 is end of arr
	}

	void printTree() 
	{ 
		for(int val : tree)
		{ 
			if(val != 0 ) cout << val << " ":
		}
		cout << endl;
	}
};
```

The idea is that we go through to the subtrees first, to get to the leaf node. 
The idea is that build takes a node, then we look at the start and end. 
Then the left node takes the first half, the right nodes takes the latter half. 
They are binary trees.

Otherwise the majority of queries on a subarray will be `O(n)`. 

Each node in the tree will represent intervals or segments of the array. 
Every leaf node is just the element of the tree I believe. 
Splitting the array can be relatively easy. 
![[Pasted image 20241208020007.png]]
![[Pasted image 20241208020010.png]]
```
class SegmentTree
{ 
	std::vector<itn> tree; // segment tree representation
	int size;

public: 
	SegmentTree(const std::vector<int>& arr)
	{ 
		size = arr.size();
		tree.resize(4 * size);// allocate enough space for the tree. 
		build(arr, 0, 0, size-1);
	}

	// build the tree recursively
	void build(const std::vector<int>& arr, int node, int start, int end)
	{ 
		if(start == end)
		{ 
			tree[node] = arr[start];
		} else 
		{ 
			int mid = (start + end) / 2; // Get the middle of the arry
			int left = 2 * node + 1; // in tree the left node is going to be here 
			int right = 2 * node + 2; // in tree the right node is going to be indexed here
			build(arr, left, start, mid); // return from left here
			build(arr, right, mid + 1, end); // return from right here
			tree[node] = std::max(tree[left], tree[right]);
		}
	}
	// query the range of [l, r]
	int query(int l, int r)
	{ 
		return query(0, 0, size - 1, l, r);
		//  goes to the helper in the private section
	}

private: 
	int query(int node, int start, int end, int l, int r)
	{ 
		// out of range
		if(start > r || end < l) return INT_MIN;

		// fully within range
		if(start >= l && end <= r)

		int mid = (start + end) / 2;
		int left = query(2 * node + 1, start, mid, l, r);
		int right = query(2 * node + 2, start, mid + 1, l, r);
		return std::max(left, right);
	}

public: 
	void update(int idx, int value)
	{ 
		update(0, 0, size - 1, idx, value);
	}

private: 
	void update(int node, int start, int end, int idx, int value)
	{ 
		if (start == end) 
		{ 
			tree[node] = value; // update leaf node
		}
		else 
		{ 
			int mid = (start + end) / 2;
			if(idx <= mid)
			{ 
				update(2 * node + 1, start, mid, idx, value);
			} else 
			{ 
				update(2 * node + 2, start, mid + 1, idx, value);
			}
		}
		tree[node] = std::max(tree[2 * node + 1], tree[2 * node + 2]);
	}
};
```

`build` recursively go through.
The base case if `if (start == end)`: 
The idea is that we start with the the whole array, node is 0, start is 0, and end is at the end of the array. 
The mid is in the middle. 
Then we move to the node that represents the left middle, then to the node that represents the right middle, remember that the root node represents the entire array as a range. 
Going to the left first for each one, meaning that `node` is now the `left` index, which is `2 * i + 1` from the last `node`, and `start` is from the very left, and end is now mid (those are the ranges). 
Right is going to be, `start` is `mid + 1` (so that are ranges don't overlap). 
The idea is that the ranges get smaller and smaller as you go through, then end will be at the very end too, this would be really easy to draw as well. 

Then in query, 

### AVL Tree
This is a self balancing tree. 
![[Pasted image 20241206231102.png]]
THIS IS NOT AN AVL TREE
![[Pasted image 20241206231112.png]]
NOT AN AVL TREE as the difference in height of the left tree and the right tree is greater than 1. 
it's named after the inventors of the tree. 
Go the wiki. 
Self-balancing. 
If the height of the left or right tree differ by more than one, then rebalancing is done to restore this property. 
Lookup, insertion, deletion is `O(log n)`. 

![[Pasted image 20241206231635.png]]
We look at balance factors of nodes. 
Using rotations in order to make sure that the height is the way it should be. 
Every node has this balancing factor, a left node is a -, and a right node is a +. 
They maintain order, despite the rotations. 
BST can degrade into a linked list in the worst case, leading to `O(n)` for search at some point. 
AVL will not run into this problem. 


#### Red-Black Tree
Again for balancing: 
Root is always black, there can be no two consecutive red nodes on a path. 
Every path from a node to its descendent null nodes has the same number of black nodes. 
It again uses rotations, allowing a less strict balance compared to AVL tree. 


#### Splay Tree
Self adjusting binary tree that brings the most recently accessed element to the root through rotations. 

### Range Query (Computer Science)
Problems consisting of efficiently answering several queries regarding a given interval of elements within an array. 
Range minimum query, finding the smallest value inside a given range within a list of numbers. 

![[Pasted image 20241207223213.png]]Given the subarray of range of an array. 
The goal is not to try and recalculate the property every single time. 
Range sum : Range Minimum Query : Range Maximum Query

We preprocess, then the time to run every query has to be faster. 
Particularly useful if we are going to query a number of times. 

Take range sum querying, meaning that we are gonna ask for the sum of a subarray. 
Then we can preprocess the array. 
```
std::vector<int> prefix_)sum(v.size());
prefix_sum[0] = v[0];
for(int i = 1; i < v.size(); ++i)
{ 
	prefix_sum[i] = prefix_sum[i - 1] + v[i]; // take the last and add the current
}
```

For Range Maximum Query and Minimum Query, then we have to have a look at segment trees. 



#### Segment Trees
Remember that these have to be binary trees, therefore our requests or `O(log n)`. 

```
class segmentSumQuery
{
    std::vector<int> _tree;
    int _n;

public:
    // remember that 4n is the necessary size for our array here
    segmentSumQuery(const std::vector<int>& arr)
    {
        _n = arr.size();
        _tree.resize(4 * _n);
        build(arr, 0, 0, _n - 1);
    }

    void build(const std::vector<int>&arr, int node, int start, int end)
    {
        // recursively build:
        if(start == end)  // range onlyo for this one element
        {
            _tree[node] = arr[start]; // or arr[end]
        } else  // if not just a range of one
        {
            int mid = (start + end) / 2; // we have to split the two

            build(arr, 2 * node + 1, start, mid); // to the left node
            build(arr, 2 * node + 2, mid + 1, end); // to the right node
            _tree[node] = _tree[2 * node + 1] + _tree[2 * node + 2] ; // then after the recursive, we come back and do this
            // This will buid the entire tree
        }
    }

    int query(int L, int R)
    {
        return query(0, 0, _n - 1, L, R);
    }

private:

    int query(int node, int start, int end, int L, int R)
    {
        // Total overlap
        if(L <= start && end <= R)
        {
            return _tree[node]; // this is clear for the total overlap.
        }

        // No Overlap
        if(end < L || R < start)
        {
            return 0; // this might need to be INT_MIN
        }

        // Partial Overlap
        int mid = (start + end) / 2;
        int left_sum = query(2 * node + 1, start, mid, L, R); // go left
        int right_sum = query(2 * node + 2, mid + 1, end, L, R); // go right
        return left_sum + right_sum;
        // being recursive means that we will visit every node to get the whole range
    }

public:
    void update(int idx, int value)
    {
        update(0, 0, _n - 1, idx, value);
    }

private:

    void update(int node, int start, int end, int idx, int value)
    {
        // if landed on node
        if(start == end) // this is when are ranged is fully closed
        {
            _tree[node] = value;
        } else
        {
            int mid = (start + end) / 2; // used in all of them
            if(start <= idx && idx <= mid)
            {
                update(2 * node + 1, start, mid, idx, value);
            } else
            {
                update(2 * node + 2, mid + 1, end, idx, value);
            }
            _tree[node] = _tree[2 * node + 1] + _tree[2 * node + 2]; // recompute, like in build
        }
    }
};
```
The `query` is the weirdest one, as we have the partial overlap, meaning that one end is in the range, however, the other won't be. 
We do this until we have a really good overlap. 
