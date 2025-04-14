Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:** The solution set must not contain duplicate combinations.

**Example 1:**

**Input:** candidates = [10,1,2,7,6,1,5], target = 8
**Output:** 
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]

**Candidates is not sorted as precondition**. 

The first thing to think about is Recursion, which is much more simple, therefore we will be thinking about the iterative (DP) position first. 
Remember that there are duplicates where `[2, 5] == [5, 2]`. So no "duplicates". 

The thing with with DP is that we are looking at all the answers that "something" to `i`, where `i` is the sum of the current vector that we are looking at. 
Think that a vector, has each index be the sum, so each index is all the vectors that sum to `i`. 
Each index maps to a `target`  in this case. Then we are building.

The difference for me with recursion is that recursion feels like filtering, comparative to iteration, where we building, and we "save" (tabulate) all that we have visited so far. 

Say the table (tabulate) here is called `dp` there `dp[i]` should house all the `vectors` that sum to `i`. 
If there are no duplicates, then we might say that we have a `set` of those `vectors` (as `set` is implemented perfectly for this, we might even use a `unorderd_set`, however, the problem here seems to have strict ordering of the output). 
We know that `dp[0]` can only have an empty `vector` - `{}`.
**The main idea with iteration and DP - we are using the smaller results to build the larger results**. That's why we call it bottom-up approach. 

**Recursion is that we have two rules - recursive rule - and the base case rule**. (just think about trees). **We are breaking down the problem**. Top Down approach.

Given that we have figured out that our `dp` should have for every `[i]` a `set` of `vectors` that sum to `i`
Therefore : `dp = vector<set<vector<int>>>;` 

Then we know that `dp[0] = {}`. 

This means that `dp` has to be of  size `target + 1`. 

Think about the goal -> that `dp[target]` has to have all the the `vectors` that sum to that `target`. 

#### First Implementation
The obvious `iteration` is to go through every number : the code there writes itself. 
`for(auto num : candidates)`

We have got to start small: 
```
candidates = [2, 3]
target = 5
dp[0] = {{}}
```

Always thinking about **Building by thinking about the target**. 
How do we start building here. 

`num = 2` then we can look whether `dp[5 - 2]` is there, no, so we can't build up from that. 
Then let's reduce the target: 
`dp[2]` also no: 
then `dp[1]` also no: 
Then `dp[0]` yes, we have that, we can build from that. 
So then `dp[2]` will have `dp[0] + 2 (as a vector)` -> therefore `dp[2] = {2}`. 

Then go to `3` : 
Then `dp[target - 3]` = `dp[2]` WHICH IS THERE!
Then we build from that and that one is obvious. 

What we have done here : we have taken `target - num` and then. 
Here we can just reduce `target` in `dp[target - num]`, however, we don't want to actually reduce `target`, therefore, let's just do `t = target`. 

```
for(int num : candidates)
{ 
	for(int t = target; t >= num; --t)
	{ 
		// now what? 
	}
}
```

This one is pretty simple, so we are looking at `dp[t - num]` then we are looking at the set of vectors that are in there. 
Therefore, if there subsets, we can build off those, and add those new values to `dp[newSum]`: 
```
for(const auto& subset : dp[t - num]) // for each vector within the set
```
We'll grab that `vector` add our number to it, then add to the set that corresponds to the sum there. 
```
for(cont auto& subset : dp[t - num])
{ 
	auto newSubset = subset; // copy the old vector (subset)
	newSubset.push_back(num); // add the current number
	sort(newSubset.begin(), newSubset.end()); // sort to avoid different order duplicates in the set
	dp[t].insert(newSubset); // t being our target, insert into dp[t]
}
```

The end is really easy, just get all the subsets that are at `dp[target]`: 
`vector<vector<int>> result(dp[target].begin(), dp[target].end());`
`return result;`

**SubSet Sum** is a staple. 

[[Coin Sum ]]
