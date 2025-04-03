### Maximum Subarray Sum
So the idea being that we want `O(n)`. 

Break down the algorithm into small parts: 
What is the way that would work given the numbers, then the brute force given the structures that we can use in the given language. 

This algorithm is known as Kadane's: 
- Add the current element to the ongoing sum (if it's beneficial) or 
- start a new subarray from the current elements (if the ongoing sum is negative and adding more values would decrease the total sum). 

```
int maxSubArraySum(const std::vector<int>& nums)
{ 
	int max_ending_here = nums[0];
	int max_so_far = nums[0];
	// go through the array once
	for(size_t i =  1; i < nums.size(); ++i)
	{ 
		// take max of this element and of so far + this element
		max_ending_here = std::max(nums[i], max_ending_here + nums[i]);
		
		// then update max_so_far and max_ending_here
		max_so_far = std::max(max_so_far, max_ending_here);
	}
	return max_so_far;
}
```

using `-S` in order to get the assembly out of the compiler instead of the machine code. 

### Compiler Optimizations
given `g++ -S test.cpp -o test.out`
```
int collatz(int n)
{ 
	if(n % 2 == 0) return n / 2;
	else return 3 * n + 1;
}
```

 the ASM looks like: 
```
test dil, 1    # check if last bit is 1 (instead of %)
jne .L2
mov eax, edi
shr eax, 31
add eax, edi
sar eax       # performs a right bit shiftu for n / 2
ret

.L2: 
lea eax, [rdi + 1 + rdi * 2]  # some weird trick for 3 * n + 1;
ret
```

We don't really need to apply a lot of tricks ourselves, as the compilers will already know how to do it. This would be really nice if we were to write the compilers, like at ARM, however, most of the time that is unnecessary. 


## Merge Sort
#mergesort
```
void merge(std::vector<int>& arr, int left, int mid, int right)
{ 
    int n1 = mid - left + 1; // size of left I think
    int n2 = right - mid;  // size of right I think


    std::vector<int> leftArr(n1);
    std::vector<int> rightArr(n2);

    // copy data to the temp arrays (that's it)
    // as Vector is just an array, we have to split it up into two temp vectors
    // this is also because we are overwriting data in arr, so our values have to be somehwere
    for(int i = 0; i < n1; i++)
        leftArr[i] = arr[left + i];
    for(int j = 0; j < n2; j++)
        rightArr[j] = arr[mid + 1 + j];

    int i = 0, j = 0, k = left;
    // while neither are above their limit
    while(i < n1 && j < n2)
    { 
        if(leftArr[i] <= rightArr[j]){ 
            arr[k] = leftArr[i];
            i++;
        } else { 
            arr[k] = rightArr[j];
            j++;
        }
        k++;
    }
    // copy any remaining elements
    // if i already at the end then we dodge this 
    while(i < n1){ 
        arr[k] = leftArr[i];
        i++; k++;
    }

    // if j already at the end then we just dodge this, at least one of these will be 
    // dodged every single time
    while(j < n2){ 
        arr[k] = rightArr[j];
        j++; k++;
    }
}

void mergeSort(std::vector<int>& arr, int left, int right)
{ 
    // if this array is greater than 1 element large
    if(left < right)
    { 
        int mid = left + (right - left) / 2;

        // recurse the left and right
        mergeSort(arr, left, mid);
        // go right from mid + 1
        mergeSort(arr, mid + 1, right);

        merge(arr, left, mid, right);
    }
}
```

## Counting Sort
#coutningsort
Is not bound by the `O(n log n)` that our comparison based sorting algorithms all fall under. 

Imagine that n is number of elements, and `k` is the range of input values. 
Imagine 0 and 100, then k = 101. 

If k is small relative to n, Counting Sort is actually faster than `O(n log n)`. 

So we go through: 
Building the frequency table -> `O(n)` that is simple, 
Then we have to go through `O(k)` -> that's going through the frequency array that we have just created, that will have to be `k` in length. 

So when n and k are fairly small. 

Sometimes the range might be ridiculous, therefore, the range of the book keeping will be a little tricky.

So really really good with low range and minimal numbers. 

```
void countingSort(std::vector<int>& arr)
{ 
	if(arr.empty()) return; // edge ase 
	
	int maxVal = *max_element(arr.begin(), arr.end());
	
	std::vector<int> count(maxVal + 1, 0); // the size of the bookkeeping array

	for(int num : arr)
	{ 
		coutn[num]++; // add to the frequency table
	}

	int index = 0; // reconstruct the sorted array
	// by going through the bookkeeping array
	
	for(int num = 0; num <= maxVal; num++) // go through the bookkeeping
	{ 
		while(count[num] > 0)
		{ 
			arr[index++] = num;
			count[num]--;
		}
	}
}
```

The second bit is a little weird:  the second `for loop`  
That's where we reconstruct: 
We for loop through every index of the bookkeeping array: 
For every index, we see if it's above 0. 
Remember that index is starting at 0, at the very beginning of the array. 
For every number that we have to input, for every frequency that we have in the bookkeeping array, we need to move one forward after that entry. 
Then we need to decrement in the count array. 


