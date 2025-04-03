## Recursive Algs
#### Generating Subsets
The correct subsets of `{1, 2, 3}` are `null {1} {2} {3} {1, 2} {1, 3} {2, 3} {1, 2, 3}` Remember that null part, therefore there are $2^{n}$ subsets, where n is length of the set. 
Think about the tree that is created when we do recursive algorithms. 
Each node is a call. 

```
                 fib(5)
               /       \
          fib(4)       fib(3)
         /     \       /     \
     fib(3)  fib(2)  fib(2)  fib(1)
    /     \
 fib(2)  fib(1)
```

```
void search(int k)
{ 
	if(k == n + 1)
	{ 
		// process subset
	} else 
	{ 
		// include k in the subset
		subset.push_back(k);
		search(k + 1); // recurse with that added element
		// take that element away
		subset.pop_back();
		search(k + 1)L;
	}
}
```
The first line of the tree - we have we add 1, then we go with 2 and add that, then we get to three, add that, then we process the subset. then we go back, where we have 1, 2, 3, take away 3, and then add that subset. 
So for every recursive call there will be one branch for each call, in this case there are two calls per, therefore two branches from each node. 

#### Generating Permutations
Where order matters.
N! permutations.
For `{1, 2, 3}` there are `(1, 2, 3), (1, 3, 2), (2, 1, 3), (2, 3, 1), (3, 1, 2), (3, 2, 1)`

The idea here is that we have a `bool chosen[n + 1]`
We meet an important concept where we recurse around a for loop. We will return to the same part of the for loop. 
```
vector<int> permutation;
vector<bool> chosen(n, false);
int n; // total numbers to permute
void search()
{ 
	if(permutation.size() == n)
	{ 
		// process
	} else 
	{ 
		if(chosen[i]) continue; // already in I think
		chosen[i] = true;
		permutation.push_back(i)
	}
}
```

Walking through it and the tree. 

```
search()
 ├── 0 → search()
 │   ├── 1 → search()
 │   │   ├── 2 → [0,1,2] (base case)
 │   │   ├── backtrack
 │   ├── 2 → search()
 │   │   ├── 1 → [0,2,1] (base case)
 │   │   ├── backtrack
 │   ├── backtrack
 ├── 1 → search()
 │   ├── 0 → search()
 │   │   ├── 2 → [1,0,2] (base case)
 │   │   ├── backtrack
 │   ├── 2 → search()
 │   │   ├── 0 → [1,2,0] (base case)
 │   │   ├── backtrack
 │   ├── backtrack
 ├── 2 → search()
 │   ├── 0 → search()
 │   │   ├── 1 → [2,0,1] (base case)
 │   │   ├── backtrack
 │   ├── 1 → search()
 │   │   ├── 0 → [2,1,0] (base case)
 │   │   ├── backtrack
 │   ├── backtrack

```
Let's go through it in our heads. 

We call search: 
Then we go into the for loop: 
i = 1. Then in our `Vector<boo>` we put 1 as true and add it, then we call again. 
Go into the for loop again, skip 1, then we go to 2. Add it, then go again. 
Skip 1 and 2, then go to 3.
then the size is right, we process and go back to that, we say that 3 is not chosen anymore and we pop it from the back. The for loop ends and we go back. then we go back again and we are on 2 in this one, therefore, we say that 2 is now not chosen, however, 3 is not chosen so now that is added, then in the next one the 2 is added at the back. 
