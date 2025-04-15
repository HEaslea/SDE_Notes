Consider : `{1, 2, 3}` - The permutations are : `(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)` - There are `n!` permutations (compared to subsets which is `2^n` when we include 0 set). 

#### Swapping

The main thing to think about here is the idea that we have all the numbers - always - and we are just swapping them over. 
Given this idea our code will look like this: 

```
void permute(std::string& s, int l, int r)
{ 
	if(l == r) // ! base case
	{ 
		std::cout << s << "\n"; 
		return; // backtrack
	}

	for(int i = l; i <= r; ++i)
	{ 
		std::swap(s[l], s[i]);
		permute(s, l + 1, r);
		std::swap(s[], s[i]); // backtrack
	}
}

// Then there's this
std::string s = "abc";
permute(s, 0, s.size() - 1);
```

Here we are going in and fixing one and permuting the rest. 

Then we need to undo that change and fix the next one. 

The most obvious thing is that when we are out of things that we can swap `(l == r)` - that means that we have hit the point that we have to add. 

Then the next part to look at is this idea in the for loop, `int i = l`, meaning that every **first** time that we enter, there will be a swap to itself, therefore, **no swap the first time that we enter that for loop**, this means the whole first time that we begin to recurse, there will be no swaps throughout the whole thing. 

Think about this that we are swapping from the back first. 

This means that the first time that we fall back in that `for` loop, `l != i` and there will be swap, as the second time we are in there, `i = l + 1`, meaning that only they will be swapped. 
Then we permute there. 
The key to remember is that we are swapping from the "back" first. 

This means that we get to `"abc"` and we add that as `l == r`. 
Then we move back, where `l=1`, however, now `i = 2`, therefore they are swapped. Then recurse. Now `l == r` therefore we add `"acb"`. 
Then we move back to that one, and reverse that swap. 
Then we leave that for loop as `i > r`. 

So then we are back to the beginning. 
`l = 0` but now `i = 1`, so `a - b` are swapped and we get `bac`
This is new, therefore,  `l = i` in that for loop. This means that the swap does nothing. 
Then recurse again, and this time `l = i` again, as this is the first time that we are in that loop and they are both 2. `l == r` then we have to add so `"bac"` is added. 
Then we go back, 3 > r, so then back again. 
Now `l = 1, i = 2` so then `"bca"`etc. etc. 

Again, we are swapping from the back. 
Every time that we go into the `for` loop, there will be no swap, hence no swap at the beginning of every permute. 

Remember that we are reversing every single time. 


### Using Bit Vector 
See Competitive Programming Book (little blue one): 
The idea being that we keep a permutation in `vector<int>`. 
And we keep a `bit vector` (boolean array) to make sure that the element is in the permutation: 
`bool chosen[n + 1]`
The idea being that we can process the permutation when it is the right size: 
```
void permute()
{ 
	if(permutation.size() == n)
	{
		// proces
	} else 
	{ 
		for(int i = 1; i <= n; i++)
		{ 
			if(chosen[i]) continue;
			chosen[i] = true;
			permutation.push_back(i);
			permute();
			chosen[i] = false;
			permutation.pop_back();
		}
	}
}
```


