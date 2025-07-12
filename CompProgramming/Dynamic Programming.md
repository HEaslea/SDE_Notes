[MIT Video](https://www.youtube.com/watch?v=OQ5jsbhAv_M)
#### What Is It? 
An exhaustive search (which seems bad) - careful brute force which is a lovely way of putting it. Try all possibilities and do it efficiently. 
Why is it called that? - Richard Bellman (Bellman-Ford algorithm) - it was to hide the fact he was doing mathematical research and then basically because it sounded cool. 
Subproblems + "re-use". 
Breaking the problem and solving the subproblems optimally. 

## Top Down  (Recursive)
#### Memoization
**Fibonacci**: 
Naive Recursive Alg: 
```
fib(n) 
if n <= 2 : f = 1
else f = fib(n - 1) + fib(n - 2)
return f
```
This is bad, therefore, to improve, we should be looking at something called memoization, as here, at the very least, every 2 of`n - 1` we get to the first `n - 2` call. 
The idea then is just remembering what we have already worked out beforehand. 
Just have a dictionary, if we already solved this one, then just get that answer. 
```
fib(n): 
if n in memo : return memo[n]
if n <= 2 : f = 1
else: f = fib(n - 1) + fib(n - 2)
memo[n] = f // make sure that we are saving at some point when we have the optimum solution
return f
```

You can really think about the tree that the recursive solution creates from this. 

`fib(k)` only recurses the first time its called, for every value of k. 

So then all we really care about is that the number of non-memoized calls. 



## Bottom Up (Iterative)
#### Tabulation
Given the coin problem - how many coins are needed to add up. 

Imagine that we have `{1, 3, 4}` - Imagine that we needed to get to `6` Then the optimum is `3 + 3` however, if we took the greedy algorithm, then it would be `4 1 1` which is not optimum - that's where the greedy algorithm is not correct and causes more issues. 

Tabulation - meaning that we have an array. 

```
int minCoints(std::vector<int>& coins, int N)
{ 
	std::vector<int> dp(N + 1, INT_MAX); // all to max int
	dp[0] = 0; // base case, 0 coins needed for amount 0
	
	for(int i = 1; i <= N; ++i) // building up the dp vector
	{ 
		for(int coin : coins)
		{ 
			if(i >= coin && dp[i - coin] != INT_MAX)
			{ 
				dp[i] = std::min(dp[i], dp[i - coin] + 1); // min of what's already there and what the this (- coin) + 1 is
			}
		}
	}
	return dp[N] == INT_MAX ? -1 : dp[N]; // if no solution, return -1 
}
```
Let's go through with: 
`coins = {1, 3, 4}, N = 6`

So we start from 1 - that's just after what we would consider to be the base case. 
When we start from 1, we go through the coins. 
In this case we get the 1, we can stop the going through slightly earlier by staying that as long as `i >= coin` otherwise it's no possible with what we have and it's just not worth going through them all. 
The second check is `dp[i - coin] != INT_MAX` - this means that we move on when we can make that. 
Then we say what is the min of `dp[i]` - so what is already there. 
or `dp[i - coin] + 1` or what is `-coin` back then + 1. 

We go through, we say that for every sum up until N, we need the optimal value for that exact coin. 

The key thing is: 
- Recognizing Subproblems - breaking the problem into smaller steps. 
- Identifying an optimal recurrence - finding how solutions then build on them, what is it that we need to do in order to get to the next subproblem. 
- Iterative or recursive

#### When and Which
Both thrive on the subproblem idea : when we have subproblems. 
Then imagine that we have recurring subproblems - meaning that if we call `fib(3)` multiple times, then we need to memoize/tabulate that. 
Ideally so we never have to calculate something that we already have. 

Recursion is great for small problems, if not a little more complicated. 
However, with recursion we have to remember that we can have stack overflow. 

Really, we don't use iterative when we find it easier to think recursively. 

**Fibonacci**
```
// recursive
std::vector<int> memo(100, -1);

int fib(int n)
{ 
	if(n <= 1) return n;
	if(memo[n] != -1) return memo[n]; // if there is a result, return it
	return memo[n] = fib(n - 1) + fib(n - 2);
}
```

```
// iterative
// we don't even need to tabulate - just keep building
int fib(int n)
{ 
	if (n <= 1) return n;
	int prev2 = , prev1 = 1, curr;
	for(int i = 2, i <= n; i++)
	{ 
		curr = prev1 + prev2; 
		prev2 = prev1; 
		prev1 = curr;
	}
	return curr;
}
```
 We don't need to optimise here, we just build, if we did optimize then we might need to save the subproblems optimizations. 
#### Counting Problems
Let's change the problem slightly to make it about how many ways there are to make that current coin. 
So imagine with the same coins `{1, 3, 4}` -> how many ways to make 0, well 1, as that is using 0 coins. 
To make 1 - well that's 1 coin we have here. 
To make 2 - well let's have a look at how many ways to make 1, it's one, there are no other ways, therefore, there's only one way to make 2. 
Then let's look at 3: How many ways to make 3 - 1, there's 1, so another 1 there, how many ways to make 3 - 3 (0), well there's 1 way, so that's 1 + 1 (as 3 - 4 is 0, there are no ways to make negative amounts). There are two ways to 3.
Then with 4 : How many ways to make 0 : that's 1, how many ways to make 1 (4 - 3) well that's 1 as well, so far that's 2: how many ways to make 4 - 1 (3), 2 ways, therefore, 4 has 4 ways to make 4. 


### Longest Increasing Subsequence
```
int LIS(std::vector<int>& nums)
{ 
	if(nums.empty()) return 0;

	int n = nums.size();
	std:;vector<int> dp(n, 1); // dp[i] stores the length of the LIS ending at i
	
	for(int i = 1; i < n; i++)
	{ 
		for(int j )
	
	}
}
```

#### Paths in a Grid
Every square in our grid `grid[x][y]` will have to be the "optimal" up until that point. 
If the only way you can move is down and/or right, then we have to look at the subproblem. 
This means that the "optimal" path to a square is the optimal path to the left OR to the one above. 
Notice that the top row can only be from moving from the left to the right, so we have to just sum that row, that can be used as a though example. 

```
int sum[N][M];
for(int y = 1; y <= N; y++)
{
	for(int x = 1; x <= N; x++)
	{ 
		sum[y][x] = max(sum[y][x-1], sum[y-1][x]) + value[y][x];
	}
}
```
`max` for when we want the largest sum. 

### Knapsack Problems
Subsets of objects that have properties. 
So imagine that we are given a list of weights, and we have to find all the possible sums that we can make from however many of those weights. 
For instance `[1, 3, 3, 5]` meaning that 2 is impossible, 7 is possible due to `[1, 3, 3]`. 
Then imagine that we have `k`, which is the number of weights that we are using. 
Again, we look at the idea of subproblems, and we say, that the current number is possible as a sum of weights if: 
`possible(x, k) = possible(x - wk, k - 1) or possible(x, k-1)` which is saying that this one is possible if it is possible to get the `x - wk` (current weight), or if we already had this sum from the last weight round. 
Adding one more usable weight will only add to the previous round, it's additive in this sense. 

```
possible[0][0] = true; // rows of possible for all of x and of k too
for(int k = 1; k <= n ; k++)
{ 
	for(int x = 0; x <= m; x++)
	{ 
		if(x - w[k] >= 0)
		{ 
			possible[x][k] |= possible[x-w[k]][k-1];
		}
		possible[x][k] |= possible[x][k-1];
	}
}
```
We can just use 1D array, and just update it every time we go over, which is pretty obvious: 
```
possible[0] = true;
for(int k = 1; k <= n; k++)
{ 
	for(int x = m-w[k]; x>= 0; x--)
	{ 
		possible[x+w[k]] |= possible[x];
	}
}
```

Here we go from right to left. 
From right to left, here we have the idea that we iterate backwards over all possible sums, meaning that if `possible[x]` then we know that `possible[x + w[k]]` is possible as well. 

We have to go backwards in order to not use a weight multiple times. 

### Permutations to Subsets
Imagine an elevator that only takes up certain weights. Minimize trips with a number of people, given their weights. 

Now, the brute force approach is to use permutations, and go through every single one, however, that would be `n!` large which is huge. 

The better approach is to use subsets, considering all groups of people, not all possible orderings from the get go, as a subset, without any consideration for the order, is essentially just a number of valid permutations. Number of subsets is $2^{n}$

If order matters - then we must use permutations, as we are looking at the orderings there. 
However, if order doesn't matter, then brute force with subsets. 

```
for(int s =1; s < (1<<n); s++)
{ 
	// initial value: n+1 rides are needed
	best[s] = {n + 1, 0};
	for(int p = 0; p < n; p++)
	{ 
		if(s&(1<<p))
		{ 
			auto option = best[s^(1<<p)];
			if(option.second+weight[p] <= x)
			{ 
				// add p t an existing ride
				option.second += weight[p];
			} else { 
				// reserve a new ride for p
				option.first++;
				option.second = weight[p];
			}
			best[s] = min(best[s], option);
		}
	}
}
```
