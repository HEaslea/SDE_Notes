An easy iterative solution: 

Given some coins `[1, 2, 5]` and a target amount `5`: 

Give the number of ways that you can make that amount. 

So think about what our `dp` should be (container where indexes are mapped to something). 

Think clearly about what the index should represent `[i]`- `i` - is the target that we are trying to sum to. 
Then `dp[i]` should be the number of ways that we can sum. 
Remember that most of the time we are returning some form of `dp[givenTarget]`.

Given that : `dp[0]` is obviously going to be 1, there is only one way to get `0` (no coins, think combinations). 

Then the iteration just goes through every coin, that is going to be the most obvious. 

`for(int coin : coins)` -> that should be obvious. 

Then the premise of the question is that we can have multiple of the same coin: therefore, the idea being that we can just use this coin over and over. 

We know that we can make the amount of the coin `dp[coin]` is `++` 
Then we know that from that coin, we can have `dp[coin + coin]` until we get to the target or past the target: 

```
for(int coin : coins)
{ 
	for(int amount = coin; amount <= target; ++amount)
	{ 
		dp[amount] += dp[amoutn - coin];
	}
}
```
Then it can get a bit confusing : however, if there are 3 ways to make the sum of 4, and we have the 1 coin in the set, then to get to 5, that would be 4 ways.

Then just `return dp[target]`

[[01 Knapsack Problem]]
