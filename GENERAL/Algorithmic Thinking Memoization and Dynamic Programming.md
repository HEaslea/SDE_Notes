### Dynamic Programming
There are two ways of doing this: Top Down (**memoization**) or Bottom Up (**iterative**). 
Therefore **memoization** is a form of dynamic programming. 

Dynamic programming is all about ordering things so that we **do not end up doing duplicated work**. 
You have a main problem (the root of your tree of subproblems), and subproblems (subtrees), where the subproblems typically repeat and overlap. 
Considering the Fibonnaci sequence: say the root is 4, then we do the subproblems, which is the sum of the fibonnaci of 3, and 2, and then they split into 2 1 and 1 0, and then the left one goes into 1 0, remains on the right as 1, then the right will stay as 1 0, and you can see how they are going to duplicate the work, unless we save that work somewhere. 

Therefore, if we imagine that we are going back up the trees, it doesn't matter which function is first, but as long as there is duplication, then we will save them. 

#### Memoization: 
Memo - ization; 
imagine that we are figuring out Fibonnaci `fib(100)`, then we get to the bottom, where we will have (at some point) `fib(2) = fib(1) + fib(0) = 1 + 0 = 1`, then we would resolve `fib(3) = fib(2) + fib(1)`, however, we already have `fib(2)` solved and cached. 
This starts at the top of the tree, and evaluates the subproblems from leaves/subtrees and back up towards the root. 

###### Tabulation
Dynamic programming can be thought of as `table-filling`, this is like memoization, but more active, and involves one additional step: you must pick ahead of time, the exact order in which you will do your computation . In the example of the Fibonnaci, we might choose to calculate the numbers in this order: `fib(2), fib(3), fib(4)` . 
Caching every single value, making computing the next one much easier. 
We can also think of this as filling up a table. 
To note that here we are building up iteratively, and not using recursion. 
Rather than tabulation, some people consider this to be dynamic. 
Here, before running any algorithm, we are considering the whole tree, then writing an alg to evaluate sub problems in particular order towards the root


#### Pros and Cons
Memoization is very easy to code, and should be the first line of approach. 
The downside of tabulation is that we have come up wit the ordering ourselves. 
Note that both can be done using recursion. 


#### Example: 
###### Coin Change
Given a list of coin denominations `coins` and a target amount `amount`, find the minimum number of coins needed to make up the amount. If it’s not possible to make up the amount, return `-1`.
`coins = [1, 2, 5], amount = 11`. 
The solution being `3` as `[5, 5, 1]`. 

###### Top Down Approach
Memoization, as we go through, using recursion, then we will memorize what we are doing. 

```
# python

def coinChangeMemo(coins, amount): 
	memo = {} # dictionary i think
	def d(n): 
		if n == 0: 
			return 0
		if n < 0: 
			return float('inf')
		# check if the result is already computed
		if n in memo: 
			return memo[n]


		# recursive case: try each coin and take minimum
		min_coins = float('inf')
		for coin in coins: 
			res = dp(n - coin)
			if res != float('inf'): 
				min_coins = min(min_coins, res + 1)

		memo[n] = min_coins # caching the result
		return memo[n]

		result = dp(amount)
		return -1 if result == float('inf') else result
```
Here we have the cool idea that we have a function in a function, that we are repeatedly calling, we have the memo in the outside scope, therefore that inner function will always have access to it. 
Here we are starting with the call to the top amount, then we are going down, and memorizing every single call that we have, this means that this is memoization and the top down approach. 

###### Bottum Up Approach
We fill up `dp` iteratively, each cell `dp[i]` represents the minimum number of coins, needed for the amount `i`, meaning that we have a certain mapping with the array (`list` in python). 
```
def coinChangeDp(coins, amount): 
	#initialize the dp array with infinity (amount + 1 is a good "infinity" bound)
	dp = [float('in')] * (amount + 1)
	dp[0] = 0 # base case: 0 coins are needed for amount 0
	
	# fill the dp array from 1 to amount
	for i in range(1, amount + 1): 
		for coin in coins: 
			if i - coin >= 0:
				dp[i] = min(dp[i], dp[i - coin] + 1)
	return -1 if dp[amount] == float('inf') else dp[amount]
```

The main differences here are that the memoization, starts from the main problem, the `amount` and moves down recursively, here we are not selecting which problems to solve in which order, they are just going and being saved. 
The bottom up solution iterative solves from the smallest subproblem (0) to the main problem `amount`. This one is typically more space efficient. 
The bottom up approach is generally faster, as we don't have the recursive overhead of the first. 


### From the Book
##### Bottom Up Approach
For the burger/homer problem:
```
void solve(int m, int n, int t)
{ 
	int result, i, first, second;
	int dp[SIZE];
	dp[0] = 0;
	// going through each one
	for(i = 1; i < t; i++)
	{ 
		if(i >= m)
			first = dp[i - m];
		else 
			first = - 1;

		if (i >= n)
			second = dp[i - n];
		else 
			second = -1;

		if(first == -1 && second == -1)
			dp[i] = -1;
		else 
			dp[i] = max(first, second) + 1;
	}
	result = dp[t];
	if(result >= 0)
		printf("%d\n", result);
	else
	{ 
		i = t - 1;
		result = dp[i];
		while(result == -1)
		{ 
			i--;
			result = dp[i];
		}
		printf("%d %d\n", result, t - i);
	}
}
```

Note here, that subproblems that we are looking at is the max number of burgers in i - n, and i - m.
Therefore, i will be either `i - n + n + 1` or `i - m + m + 1` , which seems super obvious, however, building the array like this, is very cool and easy. 

The subproblems are the same, however, we a tackling them in two directions, which is something to keep in mind. 

This means that for every `i` there is the max for `i - n` and `i - m`, meaning that we can just grab that number from the array, we get the max of that, then we have to add one, as we are looking at how many burgers are eaten up until that point. 

This is why we solve smaller subproblems first, as we know that the larger problems will require the subproblems. 
