### Recursive Backtracking

Remember that in python, we can have a function in a function, meaning that we don't have to have a `global` `subsets = []` like we did before. Obviously cannot do this in C++.

```
def genSubsets(nums : list[int]): 
	ret : list[list[int]] = []
	
	def backtrack(start, path): 
		result.append(path[:]) # this will append as a list, each list is valid therefore append early (early-processing)
		# this means that we go 
		backtrack(start + 1, path) # go to the end with this added
		path.pop() # undo and go to the end without this num added
		
	backtrack(0, []) # start at the beginning obviously
	return ret
```

Remember that the book is talking about when to process at each `node` - node being a valid subset in this case, but can just be a valid/optimal answer. 

Given `print(genSubsets([1, 2, 3])` we get `[[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]`

Remember that a valid subset is indeed an empty subset. 
And of course, a valid subset is the set itself, hence here we have `[1, 2, 3]`. 

Now that we go through it, the idea is that we process first, so that we get the `[]`. 

For every backtrack we have a `for` loop, this one is still a little tricky to go through in my head. 

The idea with the `for` loop is that we lock in the first one, then we go through all the variants of the second one, then we go through all the variants on the last one. 

Think of it like going through one, the first position, is the last to change, the furthest right position is the most likely to change next. 
We have to remember, however, there are no "duplicates" in that we have to start at the next number in the list, otherwise we end up with "duplicates" (duplicates implying there are no duplicates in the input list). 
Say if we input `[1, 3, 3]` then there will obviously be duplicates in this implementation. 
`[[], [1], [1, 3], [1, 3, 3], [1, 3], [3], [3, 3], [3]]`

This means that the `for` loop has to start from `start` in this, which is to say, the next number in the list `nums` or `input` or whatever you want to call it. 

We are also passing in `path`, which is the current `list[int]`, which is the current subset that we are building. 

In C++, we are going to need separate functions of course, however, it's much easier with references. 

```
// there's a lot in here as with all recursive functions
void backtrack(int start, const std::vector<int>& inputNums, std::vector<int>& currPath, std::vector<std::vector<int>>& result)
{ 
	// every currPath is valid therefore we push_back to result
	result.push_back(currPath);
	
	// then we have the for loop, make sure that we are starting one after the last added
	for(int i = start; i < inputNums.size(); ++i)
	{ 
		currPath.push_back(inputNums[i]);
		backtrack(i + 1, inputNums, currPath, result); 
		currPaht.pop_back(); // get ride of the last
	}
}

// this is the function that will start it all. 
std::vector<std::vector<int>> genSubsets(const std::vector<int>& inputNums)
{ 
	std::vector<std::vector<int>> result; // default ctor this
	std::vector<int> currPath; 
	backtrack(0, inputNums, currPath, result);
	return result;
}
```

#### Binary Bitmasking
The total number of subsets, is `2^n`, remembering that empty subset is valid, and a set is a subset of itself. 

The code is: 
```
def genSubsets(nums): 
	n = len(nums)
	subsets = []
	for i in range(1 << n): # as there are 2^n combinations in a subset
		subset = [nums[j] for j in range(n) if i & (i << j)]
		subsets.append(subset)
	return subsets
```

The way we generate here is going to be different to the one before. 

This one is pretty hard to follow: 

The C++ here is: 
```
std::vector<std::vector<int>> genSubsets(const std::vector<int>& nums)
{ 
	std::vector<std::vector<int>> result; 
	int n = nums.size(); // pre calc for length
	int total = 1 << n; // 2^n subsets
	
	for(int mask = 0; mask < total; ++mask)
	{  
		std::vector<int> subset;
		for(int j = 0; j < n; ++i)
		{ 
			if(mask & (1 << j)) // this is key
				subset.push_back(nums[j]);
		}
		result.push_back(subset);
	}
	return result;
}
```

Let's talk through the bitmasking part. 

If we think about the list of nums, and how a subset is just going to be a series of either in or out, meaning that a number is in `1` or out `0`. 

Therefore, if we have `[1, 2, 3]` as the input list. 
Then we can have `000` as `[]`. 
Then `001` is `[3]`
or `101` is `[1, 3]`

This represents the idea that bitmasking is just, if it's `1`, we keep it, if it's `0` then we don't, then it becomes very clear as to what is going on here. 

