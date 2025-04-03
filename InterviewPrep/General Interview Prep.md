### Reversing a String
```
#include <iostream> 
#include <algorithm> 
#include <string> 

int main() 
{
	std::string str = "AmazonInterview";
	std::reverse(str.begin(), str.end());
}
```

Now this will get you into trouble, however, it will be important to note, if you just write it super quickly and say that is what we are trying to emulate. And say that this `std::reverse` will be written by much better writers than myself, and we should use these whenever we can. 

Or just move from the back and the front and swap whatever is there: 
```
std::string reverseString(std::string& str)
{ 
	int left = 0; 
	int right = str.lenght() - 1; // as we look at index of last

	while(left < right)
	{ 
		std::swap(str[left], str[right]);
		left++;
		right--;	
	}
}
```
Then look for edge cases, and simple cases such as `""`. 
We would also need to ask about in place, or a whole new string. 

Then really impress with templates: Might add this at the end: 
```
template <typename Container> 
void reverseContainer(Container& cont)
{ 
	auto left = container.begin(); 
	auto right = container.end();

	// then using the two pointer technique
	if(right != container.begin()) // there is something there
	{ 
		-- right; // adjust so that is valid as end is one past	
		while(left < right)
		{ 
			std::swap(*left, *right);
			++left; 
			--right;
		}
	}
}
```

Then `std::reverse` is what we are implementing, which looks something like this: 
```
template<class BidirIt>  // Bidirectional Iterator
constexpr // since C++20 this is fine
void reverse(BidirIt first, BidirIt last)
{ 
	using iter_cat = typename std::iterator_traits<BidirIt>::iterator_category; 

	if constexpr (std::is_base_of_v<std::random_access_iterator_tag, iter_cat>)
	{ 
		if(first == last)
		{ 
			return;
		}
		for(--last; first < last; (void)++first, --last)
			std::iter_swap(first, last);
	}
	else 
		while(first != last && first != --last)
			std::iter_swap(first++, last);
}
```

### Find Pair With Sums: 
```
bool fidnPairWithSum(const std::vector<int>& nums, int target)
{ 
	std::unordered_set<int> seen;
	
	for(int num: nums)
	{ 
		// check if complement in given set
		if(seen.find(complement) != seen.end())
			return true;
			
		seen.insert(num);
	}
	
	return false; 
}
```
```
template <typename Container, typename T> 
bool findPair(const Container& nums, T targ)
{ 
	std::unordered_set<T> seen; // for O(1) lookup
	for(const auto& num : nums) //using auto here
	{ 
		T complement = target - num;
		if(seen.find(complement) != seen.end())
			return true;
		seen.insert(num);
	}
	return false; // if out of this, then not found
}
```

### Kth Largest Element
Min heap - optimal for large input. 
Then I would ask if we wanted to implement that ourselves, or we could just use `<queue>`
```
int findKthLargest(std::vector<int>& nums, int k)
{ 
	std::priority_queue<int, std::vector<int> std::greater<int>> minHeap;
	
	for(int num : nums)
	{ 
		minHeap.poush(num);
		if(minHeap.size() > k)
		{ 
			minHeap.pop(); // remove the smallest element
			// remember that we need to remove a certain amount, might be worth writing this out to see
		}
	}
	return minHeap.top(); // Root is the Kth largest
}
```

**Partition Based** **Quick-select Algorithm**
This is very similar to quicksort (we will go over all the sorting stuff soon). 

We choose a pivot and partition the array. 
If the pivot equals `n - k` then we've found the Kth largest. 
If not, `pivot > n - k` or `pivot < n - k`. 

```
int partition(std::vector<int> & nums, int left, int right)
{ 
	int pivot = nums[right];
	int i = left; 
	for(int j = left; j < right; ++j) // move j from left to right
	{ 
		if(nums[j] >= pivot)
		{ 
			std::swap(nums[i], nums[j]); 
			i++; // i is where we will be swapping them over
		}
	}
	std::swap(nums[i], nums[right]);
	return i; 
}

int quickselect(std::vector<int> & nums, int left, int right, int k)
{ 
	int pivotIndex = partition(nums, left, right);

	if(pivotIndex == K)
	{ 
		return nums[pivotIndex];
	} else if(pivotIndex < k)
	{ 
		return quickselect(nums,pivotIndex + 1, right, k); // do the right
	} else 
	{ 
		return quickselect(nums, left, pivotIndex - 1, k); // do the left
	}
}
int findKthLargest(std::vector<int>& nums, int k)
{ 
	int n = nums.size(); 
	return quickselect(nums, 0, n - 1, n - k); // get the kth largest at n - k
}
```

For the Kth largest, like the quicksort, we can just change in the `quicksort` recursive part, we only need to recurse, if we have not found the k yet: 
```
int quickselect(std::vector<int>& nums, int low, int high, int k)
{ 
	// normal partition
	int pivotIndex = partition(nums, low, high);
	if(pivotIndex == k)
	{ 
		return nums[pivotIndex];
	} else if(pivotIndex < k)
	{ 
		// do the right side
		return quickslect(nums, pivotIndex + 1, high, k);
	} else 
	{ 
		// do the left
		return quickselect(nums, low, pivotIndex)
	}
}

int findKth(std::vector<int>& nums, int k)
{ 
	int n = nums.size(); 
	return quickselect(nums, 0, n - 1, n - k);
}
```
`n-k` as we are getting `kth` largest. 

This is known as quick select. 

### Heap
Remember that we need to do this on an array. 
The complete property of an array, left-adjusted allows us to use it like this. 
### Sliding Window
The idea of having two pointers/indexes that just move. 
Finding a subarray or substring that satisfies certain conditions, longest, shortest, or sums to a specific value.
Such as length of Longest Substring Without Repeating Characters
```
int llswrc(std::string const& s)
{ 
	std::unordered_set<char> seen;
	int left = 0, right = 0;
	int maxLength = 0; 
	while(right < s.length())
	{ 
		if(seen.find(s[right]) == send.end())
		{ 
			seen.insert(s[right]);
			right++;
			maxLength = std::max(maxLength, right - left); 
		} else  // if found
		{ 
			seen.erase(s[left]); // get rid of left hand char
			left++;
		}
	}
	return maxLength;
}
```

### Sliding Hash
The sliding window is very similar to a sliding hash. 

### Reverse a String
Very simple. 
```
std::string reverseString(std::string const& a)
{ 
	return std::string(a.rbegin(), r.rend());
}
```

#### General
```
bool isAnagram(std::string const& a, std::string const& b)
{
    std::unordered_map<char, int> dict;
    for(auto& c : a) dict[c]++;

    for(auto& c: b)
        if(--dict[c] < 0)
            return false;

    return true;
}

template <typename Inner>
bool isDupe(std::vector<Inner>& arr)
{
    std::unordered_set<Inner> check;
    for(auto& num : arr)
    {
        if(check.find(num) != check.end())
            return true;
        check.insert(num);
    }

    return false;
}

int firstUniq(const std::string& s)
{
    std::unordered_map<char, int> freq;

    for(auto& ch : s)
    {
        freq[ch]++;
    }

    // then go the string and see if that letter has a freq of 1
    for(size_t i = 0; i < s.size(); i++)
    {
        if(freq[s[i]] == 1)
            return i;
    }

    return -1;
}

// std::reverse
std::string reversestring(std::string const& a)
{
    return std::string(a.rbegin(), a.rend());
}

bool isPalindrome(std::string const& a, std::string const& b)
{
    if(a.size() != b.size())
        return false;
    std::reverse(std::begin(b), std::end(b));
    return (a == b);
}
```

### Max Sum of Subarrays
```
template <typename Cont>
void subMax(Cont& container, size_t k)
{ 
    using Inner = typename Cont::value_type;

    Inner max;
    Inner windowSum;

    std::size_t left = 0;

    // get first window
    for(int i = 0; i < k; ++i)
        windowSum += arr[i];

    max  = windowSum;

    for(int i = k; i < arr.size(); ++i)
    { 
        windowSum += arr[i] - arr[i - k];
        max = std::max(windowSum, max);
    }
    
    return max;
}
```


### Divisible Sum Pairs
You are given an array, and then a target, if you add two numbers from the array, then % the target, then we get 0, then we `result++`; 
Then we return `result`;

The optimized approach for `O(n)`: is looking like: 
```
std::vector<int> count(k, 0); // k 0's
for(int num : arr)
{ 
	int remainer = num % k; // 
	int complement = (k - remainder) % k; 
	result += count[complement];
	count[remainder]++;
}

return result;
```

## Cool Ways of Doing FizzBuzz

Think nesting ternary operators
```
// the Simplest way
// give a number n, say we go up to 100

for(int i = 0; i <= 100; i++)
	std::cout << (i % 3 == 0? "Fizz" : "") + (i % 5 == 0? "Buzz" : (i % 3 ? std::to_string(i) : "")) << "\n";
```

Remember that
Divisible by 3 : print Fizz
By 5 : print Buzz
if not by 5, then check by 3 as well, 
`std::cout << (i % 3 == 0? "Fizz" : "") + (i % 5 == 0? "Buzz" : (i % 3 ? std::to_string(i) : ""))`


Going the long way: 
```
// Dictionary

std::unordered_map<int, std::string> fizzMap = {{3, "Fizz"}, {5, "Buzz"}};

for(int i = 1; i <= n; i++)
{ 
	std::string output; 
	// structured bindings
	for(const auto& [key, value] : fizzMap)
	{ 
		if(i % key == 0) output += value;
	}
	std::cout << (output.empty() ? std::to_string(i) : output) << "\n";
}
```

Omg this is almost pythonic -> 