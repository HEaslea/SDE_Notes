## Climbing Stairs

#### Statement
Imagine you have a staircase with `n` steps. You can climb either 1 or 2 steps at a time. Write an iterative program to calculate how many distinct ways there are to reach the top of the staircase

There are two ways that we can go up the stairs, 1 or two steps at a time : in my head they are the recursive calls and then we say `recurse(step + 1) or recurse(step + 2)`, therefore, I have to think that the stair we are on will be "arrivable" from the stairs that are one below and two below. 

Think about the recurrence here, `dp[i] = z`  where `z` is the number of ways to get to that step. 
This is of the recurrence relationship that looks like `dp[i] = dp[i - 1] + dp[i - 2]`. 

Remembering this fact as we go through : that index 0 has to be for no steps. therefore our `dp` has to be of size `n + 1` for `n` steps. 

Same with recursion : there are base cases : which we think about, there are 0 ways to get to step 0, there is 1 way to get to step 1, there is then two ways to get to step 2. 
We could do step 2 and step 1 and start from there, however let's just simplify from step 0 and step 1, which is fine. 

Written in C++: 
```
int getStairs(int target)
{ 
	std::vector<int> dp(target + 1, 0); // could just get to 1

	dp[0] = 1;
	dp[1] = 1;

	for(int i = 2; i <= target; ++i)
	{ 
		dp[i] = dp[i - 1] + dp[i - 2];
	}
	
	return dp[target];
}
```
Really really simple. 


### Coin Change Problem
Given a list of distinct coin denominations (e.g., `[1, 2, 5]`) and a target amount, determine the **number of ways** to make up that amount using any number of coins. You may use each coin denomination as many times as you like.

`dp` will hold the number of ways to make up that amount using any number of coins. 

Think about this: what do we have at any  given amount. Say the amount we want to now about is 10: and the coins that we have `[1, 2, 5]` -> The number of ways to then make 10 is the number of ways to make 9, 8 and 5. 
Then the number of ways to make 9 is the number of ways to make to 8 + 7 + 4.
It seems that we only need to iterate through `[x]` which is the amount that we are summing to. 

Remembering that we are returning to the form of `dp[x]` -> so then the `x` has to be the amount that we are targeting. As we have`dp[target]`. That figures itself out.

**Recursive** - that recursive way in my head is that we have a value - we use memoization and just add whenever we reach a certain value - and from any given value, we can add the first coin, or the second, or the third etc.

The base cases are going to be : for every coin in `coins`, `dp[coin] = 1`, I believe everything else will be 0 at the beginning. 

Therefore: 
`WaysToMake = dp[i - coin_1] + dp[i - coin_2] + ... + dp[i - coin_n]`
