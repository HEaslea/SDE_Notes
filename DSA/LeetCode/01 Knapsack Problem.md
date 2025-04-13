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

