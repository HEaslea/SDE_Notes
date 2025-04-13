[[Coin Sum]]
[[Combination Sum II]]
Another iterative problem: 

You have a set of items, each with a weight and a value. Given a maximum weight capacity W, the goal is to select a subset of these items such that the total weight is less than or equal to W and the total value is maximized. Each item can either be taken or not (i.e., 0 or 1 copy of each).

Think about what we can do with either item : (similar to recursion) we can either take it or not. 

Then we need the `dp` - the container (whatever that might be, and that will have to be suited for the question) - that will hold all the building blocks to the question. 
Think about iteration where we can split and conquer - however, we are split and building (not separate to). 

###### Dimensions of `dp`
Think about the dimensions of the `dp` - this is where it begins to get complicated: 
Remember this `dp[x][y] = z`, there are 4 variables - 4 things to represent within this. 
There is `dp` which is the container (of type `decltype(z)`). 
`z` is going to have to be what we are trying to solve for eg. how many ways (so `int` and the number of ways), or maximum value therefore `z` is the maximum, pretty obvious. 
`x` and `y` relate to some state that we are following. In coin sum, instead of these we had `i` (any var there), then `i` represents the `target`. 

We know that `z` has to be what we are returning - that's the answer to the underlying question there. 

Then `dp` depends on how the question is phrased - such as no duplicates - we might use a set etc. 

The way that I see the `x, y` is thinking about the matrix that it creates and thinking, **for every x, there are y answer(values)**

Here, we have `x` - represents the number of items considered (from 0 to n);
And `y` - represents a particular weight (from 0 to `capacity`); 

**For every number of items (each i) there are values for every possible capacity, w**. 

For every`i` we have computed the maximum value for each weight `w`. 

Remembering that `z` is the answer (the end product). 

Another example is Levenshtein Distance (Edit Distance): 
`dp[i][j]` where `i` represents the length of one string (or the first `i` chars) and `j` represents the length of the other string (or the first `j` chars). For every `i`, there are `j` values that store the edit distance. 

Now that we have those two, we know for a fact that we will have to iterate over them -> 
### Iterating and Implementing
Given that we are  going to iterate over `x` and `y` (that is a given in these questions for the most part): 
Let's start building: 
Given `i` which is the number of items: 
If there are 10 `weights` there are 10 items (therefore 10 values as well, every item has both). 
Giving:
`n = weights.size();`
`for(int i = 1; i <= n; ++i)`

Here's the weird part that we need to get our heads around, we also move the variable of `weight`, so we have two variables that move, the two iterables - the number of items, and the max weight that we have. 
**Remember, we are building for all answers and then just returning the one that we want**. 

We have to iterate there. 

##### Base Cases
Think about the base cases here: where we have 2 dimensions: for `dp[0][j]` - think about what the `0` means : that we have 0 items : therefore the value for every `j` here is going to be 0 (as there are 0 items). 

Then for `dp[i][0]` - this means there are `i` items and the maximum value is `0` for all items


### Recurrence Relation
$$F_n = F_{n - 1} + F_{n - 2}$$
Being a linear recurrence, the $n^{\text{th}}$ term is equated to a linear function of the `k` previous terms. 
The example above is the Fibonacci sequence. 
Where the order `k` is two and the linear function merely adds the two previous terms. 
There are constant coefficients (1 and 1) that do not depend on $n$. 
One can express the general term of the sequence as **closed-form expression** of $n$. 
Linear recurrences with polynomial coefficients depending on $n$ re also important, many common elementary functions and special functions have a Taylor series, whose coefficients satisfy such a recurrent relation. 

This concept can be extended to multidimensional arrays, that is, indexed families that are indexed by tuples of natural numbers (basic arrays that we see commonly in Computer Science).

**Recurrence Relation** : is an equation that expresses each element of a sequence as a function of the preceding ones. 

With an example where only the immediate preceding element is involved, this might have the form. 
![[Pasted image 20250413182440.png]]

This is very important for out DP (iterative) as we are using the past elements in order to build to a new sequence. 

This is why we always need a base case, same with recursion (where we "fall" onto the base case). 
But **we need the base case sooner in iterative DP** . 

Remembering that `dp[i][j] = z` - where `z` is the maximum value achievable with the first `i` items considering a knapsack capacity of `j`. 

Where `i` and `j` are going to be the variables of the questions: 

The idea being that we loop over each item `i` from 1 to `n` and each capacity `j` from 1 to `w`. 
For each cell `dp[i][j]`, consider two possibilities: 
It's easier for me to think about it recursively (what to do at each item)
1. Do not include the current item: 
	- The maximum value remains as `dp[i - 1][j]` (the same as when there is 1 less item).
	
2. Include the current Item (only if it fits, `w[i - 1] <= j` (we stay within capacity)): 
	- The value will become the value of the current item `v[i - 1]` plus the maximum value achievable with the remaining capacity: ie. `dp[i-1][j - w[i - 1]]`, when you "sound it out" - take the maximum value achieved when we have one less item, with the capacity that is `j` , minus the the current item's weight. 

![[Pasted image 20250413185928.png]]
It can be represented with this recurrence relationship. 

### The Code
**Python**: 
```
def knapsack(values, weights, w): 
	n = len(values)

	# Create a DP table with (n + 1) rows and (W + 1) columns, init'd to 0
	dp = [[0 for _ in range(W + 1)] for _ in range(n + 1)]

	# Build the table row by row

	for i in range(1, n + 1): 
		for j in range(1, W + 1)
			if weights[i - 1] > j: 
				# Current Item I cannot be included because it's too heavy
				# Therefore we just take dp[i - 1][j]
				dp[i][j] = dp[i - 1][j]
			else: 
				# consider adding or not adding depending on the max
				dp[i][j] = max(dp[i - 1][j], values[i - 1] + dp[i-1][j - weight[i - 1]])
				
	# The max value achievable with capacity W is in dp[n][W].
	return dp[n][w]
```
