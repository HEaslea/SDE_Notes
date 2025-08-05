The intuition here is correct, two pointers. 
We are finding the valleys, and recording the difference between the lowest wall and the bottom of the valley. 

My reasoning here was that we have the two walls of the valley, meaning the highest two parts, on the edges of a valley, where each bit in between is `<` both walls. 

This means that all the water that is trapped between the two walls, is the difference between that height and the smallest of the two walls.
`std::min(leftWallHeight, righWallHeight) - height[i]`. 
The height at `i`. 

Then my intuition says that the next part, along with this idea is just going to be a bunch of conditionals on the current state of the indexes. 

I'm going to give it a go before I do some research, knowing that this two pointer system is a good way to start. 

The problem is laid out to ask the difference in height between the lowest wall of the valley that it's in, and the height of the block we are on currently.

`water[i] = std::min(leftWallHeight[i], rightWallHeight[i]) - height[i]`

We might say that for every index we go to the left, and we go to the right to see which walls we are using. 

What I didn't think about is the idea of pre-calculating. 
We pre-calculate for each index what the left and right wall is. 

[Trapping Rain](https://www.geeksforgeeks.org/dsa/trapping-rain-water)

The idea being that for index `i` we take the max on the right and the max on the left. 

### Naive Approach
The writing above shows us the naive approach, we are going left from i and right from i for every i, therefore we have `O(n^2)` time. 

```
int trap(std::vector<int>&arr)
{ 
	int ret{};

	for(int i = 0; i < arr.size(); i++)
	{ 
		int left = arr[i]; // then we need to go left from i, or from the beginning to i
		for(int j = 0; j < i; j++)
			left = max(left, arr[j]);

		// same for right
		int right = arr[i];
		for(int j = i + 1; j < arr.size(); j++)
			right = max(right, arr[j]);

		ret += std::min(left, right) - arr[i]; 
	}
	return ret
}
```

This is naive, as you a recalculating over and over again to the same answers. 
This should be obvious, however, after taking a break for a while, this wasn't abundantly obvious. 

The issue here is that we get a time limit exceeded, therefore we need something a little more quick, and little more clever. 

Here, we are using very little space for this. 
Therefore, we could lose some space optimization in order to gain some speed. 

### Filling other arrays
#### Prefix and Suffix Maximum Arrays
Prefix maximum arrays store the maximum value that we have seen so far. 

Suffix maximum arrays store the maximum value from right to left, so just going the opposite way. 

So first we can make the prefix maximum array. Then we can make a suffix maximum array and just use the two there. 

This will give `O(3n) = O(n)` : 

We just need to more vectors here of `n` size. 
```
int trap(std::vector<int>& height)
{ 
	int n = arr.size(); 

	std::vector<int> left(n);
	
	std::vector<int> right(n);

	int ret = 0;

	left[0] = height[0];
	for(int i = 1; i < n; i++)
		left[i] = std::max(left[i - 1], arr[i]);

	right[n - 1] = height[n - 1];
	for(int i = n - 2; i >= 0; i--)
		right[i] = std::max(right[i + 1], arr[i]);

	// then we go through as usual
	for (int i = 1; i < n - 1; i++)
	{ 
		ret += std::min(left[i], right[i]) - arr[i];
	}
	return ret;
}
```

This one will be accepted, it's just quick enough, however, there is still room for improvement.

### Two Pointers 
This is what we thought intuitively, however, we couldn't get through the ideas. 
It was good to explore the prefix maximum and suffix. And also the initial one, that was the slowest, as the idea was important to think about : that we need the highest wall on the left and the right for each index `i`. 
Having seen the visualization of it, it seems very obvious. 

We have two pointers, one that moves to the right (`left`) and gets the max so far, and one that moves to the left (`right`).

Then it's clear that we just do the same. 
We stop when the two pointers meet. 
We just follow what is the max up until that point.

```
int trap(std::vector<int>& arr)
{ 
	int ret{};
	int left = 1;
	int right = arr.size() - 2;

	int lMax = arr[left - 1]; // the very first number
	int rMax = arr[right + 1]; // the very last number

	while(left <= right)
	{ 
		if(rMax <= lMax) // if they're the same, then the calc is the same for both
		{ 
			// then we add the water for arr[right]
			res += std::max(0, rMax - arr[right]); // as rMax is the smallest

			// then we need to update rMax
			rMax = std::max(arr[right], rMax); 
			right -= 1;
		} else {  // if lMax < rMax
			res += max(0, lMax - arr[left]); // if 0 or < 0, that's fine, we just add either

			lMax = std::max(lMax, arr[left]);
			left += 1;
		}
	}
	return ret
}
```

That's the one that everyone is using in their solutions, which is sad, because it's become a memory game. 
However, there are huge takeaways for this, in terms of looking at how we progress through implementations. 

S