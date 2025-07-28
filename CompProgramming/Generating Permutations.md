The general idea with permutations is that we have `n!` permutations. 

If you were doing it in prog, then just use `itertools`

```
import itertools

data = [1, 2, 3]

perms = list(itertools.permutations(data))
```

This would be the most likely way of doing it. 

And similar in C++: 
```
std::vector<int> data = {1, 2, 3};

std::sort(data.begin(), data.end());

do{ 
	for(int num : data)
	{ 
		std::cout << num << ' ';
	}
	std::cout << "\n";
} while(std::next_permutation(data.begin(), data.end()));
```

Less simple, but still this does 99.9% of the work for us.

Similar to [[Generating Subsets]] : there is reward to going through the code in how this works. 

Again, we use a backtracking method. 

The key to remember with permutations, there are `n!` possible permutations. 

And remember as well, that in Python, we can have a function in a function, therefore we do not need some global variable. 

One of the main ideas that we are floating in the book is that we have a vector of remaining elements that we could add. 

### Recursive Backtracking
Again, similar to subsets, we have to recurse with changing lists and a current tracker of where we are. 

```
def permute(nums : list[int]): 
	result = [] # means taht we don't need a global and the inner backtrack is encapsulated here

	def backtrack(path : list[int], remaining : list[int]): 
		if not remaining:  # there is nothing left to put in
		# meaning this is a valid size and valid permutation
			result.append(path)
			return # end of this path and go back
		for i in range(len(remaining)): 
			backtrack(path + [remaining[i]], remaining[:i] + remaining[i+1:]) # remember slicing is [)

	backtrack([], nums)
	return result


print(permute([1, 2, 3]))
```

There are some cool ideas here: 
`remaining[:i] + remaining[i + 1:]` let's see that in action

```
nList = [1, 2, 3, 4, 5]

for i in range(0, len(nList)): 
	tList = nList[:i] + nList[i + 1:] # [) non inclusive for slicing, same as range
	print(tList)
```
```
[2, 3, 4, 5]
[1, 3, 4, 5]
[1, 2, 4, 5]
[1, 2, 3, 5]
[1, 2, 3, 4]
```

We are avoiding the `i` here every time, so in the code above, that is what we are doing, we are avoiding that element in this case, we can obviously widen that by changing the 1. 

We also do `path + [remaining[i]]` just to make sure that we are adding two lists together, appending them together in reality, even though it only has one element. 


We essentially just have a `for` loop that is going through each element that is `remaining`. 

Then the backtrack means that we come back to the point where that element was not added to `path`. 

This seems much more simple than the subset one, because all the permutations are the same length. 

The C++ is slightly longer, however, still simple: 
We use a `std::vector<bool>` in order to see if that element is in the list or not, `1` meaning it has been used here.
```
void backtrack(std::vector<int>& path, std::vector<bool>& used, 
	std::vector<int>& nums, std::vector<std::vector<in>>& result)
{ 
	if(path.size() == nums.size()) // if they're the right size, then (hopefully) valid perm
	{ 
		result.push_back(path);
		return;
	}

	for(size_t i = 0; i < nums.size(); ++i)
	{ 
		if(used[i]) continue; // we can't use this one in this path
		used[i] = true; // now we have used this element at index i
		path.push_back(nums[i]); // then get the element and append it
		backtrack(path, used, nums, result);
		path.pop_back(); // undo the appending after the backtrack
		used[i] = false; // undo what we have done
	}
}

std::vector<std::vector<int>> permute(std::vector<int>& nums)
{ 
	std::vector<std::vector<int>> result;
	std::vector<int> path;
	std::vector<bool> used(nums.size(), false);
	backtrack(paht, used, nums, result);
	return result;
}
```

Undoing after the backtrack is a common thing throughout these algs. 

Now this is the main concept here. 

There is no special way of doing this really like with Subsets, where we can use bitmasking. 

The only real way to improve this is with in-place swapping, without creating a new list every single time. 

```
def permute(nums):
    result = []

    def backtrack(start):
        if start == len(nums):
            result.append(nums[:])
            return
        for i in range(start, len(nums)):
            nums[start], nums[i] = nums[i], nums[start]
            backtrack(start + 1)
            nums[start], nums[i] = nums[i], nums[start]  # backtrack

    backtrack(0)
    return result

print(permute([1, 2, 3]))
```


```
#include <iostream>
#include <vector>

void backtrack(int start, std::vector<int>& nums, std::vector<std::vector<int>>& result) {
    if (start == nums.size()) {
        result.push_back(nums);
        return;
    }
    for (int i = start; i < nums.size(); ++i) {
        std::swap(nums[start], nums[i]);
        backtrack(start + 1, nums, result);
        std::swap(nums[start], nums[i]); // backtrack
    }
}

std::vector<std::vector<int>> permute(std::vector<int>& nums) {
    std::vector<std::vector<int>> result;
    backtrack(0, nums, result);
    return result;
}

int main() {
    std::vector<int> nums = {1, 2, 3};
    auto result = permute(nums);
    for (const auto& p : result) {
        for (int n : p) std::cout << n << ' ';
        std::cout << '\n';
    }
}
```

