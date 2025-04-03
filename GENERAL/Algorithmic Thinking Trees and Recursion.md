Started off by looking at binary trees. 

The idea here starts off by looking at the halloween haul problem. 

You are given a **binary** tree and you have to go through the tree, to get to the houses, that have the numbers in, if they don't have a number they are not houses, etc. 

The idea being that we have to see the best way to get all the candy is, in that you have to get all the candy, however, what is the most efficient way of doing it. 

The thing with the binary tree is that in order to get to every single leaf, we will have go back to the node at least once, unless the tree is essentially empty. 

How do we represent a tree. 

```
template <typename T> 
struct Node
{ 
	T data;
	Node* left; // Remember that name injection means that this is Node<T>
	Node* right; // Again name injection means that this is Node<T>
};
```
Every `Node` instance found in struct here will have to be `Node<T>` there is no alternative. 
Defining the node to be of type `T`, then that they are pointing to other `Node<T>` for left and right, means that we are locking in the type of that instantiation, based off the first node, this does leave room for human error. 
Imagine that someone didn't get the memo and then all of a sudden wanted a node of a string, from and `Node<int>`, they wouldn't be able to and they would  get an error. 
Well shucks, our `Node` struct didn't really make that clear. 
This is tangential af, however, something to bear in mind when we look at the implications of all this. 

You can also have: 
```
struct Node
{ 
	std::variant<int, double, std::string> data
	Node* left;
	Node* right;
};
```
With this you can instantiate a Node with this super cool idea: 
```
Node s{.data = "Hello there", .left = nullptr, .right = nullptr};
Node f = {"Hello There", nullptr, nullptr};
Node g{"hi", nullptr, nullptr};
Node c{"hi"};
// Node e("Hello there", nullptr, nullptr);
```

The last doesn't work as e is trying to use the constructor, however, the `Node` is not a regular struct with a constructor, it's an aggregate. 

However, all of the above work. 
I believe if they are not instantiated, then they will be default valued. 
Do this to test that theory: 
```
struct Test
{ 
	std::string a;
	int b; 
	char c; 
	double d; 
	void output() 
	{ 
		cout << a << "   " << b << "    " << c << "   " << d;
	}
};

Test t{};
t.output(); 
```
they are all default valued : a is empty, b is 0, c is empty, d is 0 (0.0). 
What's the thing about binary trees, they're cool, but each node can only have 2 children at most. 
### Binary Tree Properties
The maximum number of nodes that can be at level l is $2^l$. 
The maximum number of nodes in a binary tree of height `h` is $2^h$. 



#### Recursion
[[GENERAL/Recursion]]
The idea with recursion, is not to think about how the stack frame will look with all the calls to the same function etc, however, it would be to think about the rules that they dictate, the base case(s) and the recursive cases, making up all the rules. 
The rules are implanted using `return` that is found within the functions. 

Some examples would be really useful here, since we are working on binary trees, we can see how we build these rules: 

We want to recursively find the sum total of candy in the tree: 
> **Rule 1**: If we are at a house (if we are at the root and it is a house as well), then the total amount of candy (to add) is the candy at that house. 
> **Rule 2**: IF the root of the tree is nonhouse (if we are at a nonhouse), then the total amount of candy in that tree is equal to the total amount of the left subtree plus the right subtree. 

The idea is that instead of thinking what kind of stack frame we are looking to create, we want to be able to define some rules about how we move through a data structure. 

```
int tree_candy(node* tree)
{ 
	if(!tree->left && !tree->right)
		return tree->candy;
	return tree_candy(tree->left) + tree_candy(tree->right);
}
```
Rule 1 and 2 are directly represented here. 
You can see how the recursive nature is great for trees, where we don't have the pointer to the parent node, it will move back to the node above, as that is earlier in the call stack. 

Here's another in order to find how many nodes that we have in the tree: 
```
int tree_leaves(node * tree)
{ 
	if(!tree_left && !tree->right)
	{ 
		return 1; // if a leaf node, then add one
	}
	return 1 + tree_nodes(tree->left) + tree_nodes(tree->right);
}
```
Here it is important to note the plus one, meaning that we have to add 1, to ensure that we have visited that node. 

Again, think about the rules: 
Rule 1: if the node we are on is a leaf node, then we just return 1, to show that we have visited that node. 
Rule 2: if we are not on a leaf node, then we need all the nodes of the two subtrees we are on, added together, **along with the node that we are currently on (hence the +1)**. 

If we miss the +1: 
```
return tree_leaves(tree->left) + tree_leaves(tree->right);
```

This would only return the the leaf nodes that are in the tree, as we are not adding 1 for every node that we visit, only for the leaf nodes. 

Another one is returning the height of the tree, meaning the longest subtree of the entire tree, that is the height: 
What would the rules be: 
Let's have a think, for every node that we are on, we take the longest subtree (max of right or left) and then we have to add one for the node that we are one. If we are on a leaf node, then we would just have to plus one, meaning that if the whole tree is just a node, then we just return 0, that's the base and the edge case in one there: 
```
int tree_height(node* tree)
{ 
	if(!tree->right && !tree->left)
		return 0;
	return 1 + max(tree_height(tree->left), tree_height(tree->right));
}
```
Beautiful, such a nice way of thinking about recursion.

#### Reading Tree Input
This is just a problem with the questions, that we have to get an input, therefore we have to be able to put them all together. 

Read the book at pg 59. 
The idea is that every parenthesis is a node, and any numbers within the parenthesis is a non house node. 

Therefore: `((4 9) 15)` This means that there is a root node (the first `()`) and that has a child 15, and a child node that also has the nodes (denoted by the `()`)which has two children 4 9. 


### Why Use Recursion
It's never easy to really know, with 100% certainty. 
> Whenever a problem can be solved by combining solutions to smaller subproblems, you should try recursion. 

This is going to be easier when we think about memoization and dynamic programming. 
If we think that a problem is made easier by knowing the answer to the subproblems, such as, knowing the total candy of a tree, is made easier by knowing the total candy in the subtrees. 


#### Descendent Distance
We are given a tree of descendants, and a distance, we have to find how many people there are that distance from the root. 
We can say that the number of descendants at distance d is the number of nodes that can be found that number of edges away. 

The input is the input here and you would find a way to make it work. 

#### The Hockey Rivalry Problem
The larger thing that we are running into here is that we have to figure out what is going to be recursive. 
All of these are optimization problems, given sets of numbers. 
When we move through the sets in a certain way, then we will find the right succession of numbers, the right combination to either get the **maximization** or **minimization** (the minimal or maximum). 

Then we have to think about all the subproblems that come out of these. 
The recursive element here is that for every team that we pair up, there is a smaller subset of teams that are left to be paired up. 

What are the four options when we look at how this can become recursive: 
The match that we are on is the rivalry; then we get the max; then we have to move one game to the left, both --i and --j. However, there is also the possibility that i stays in place and j moves, and vice versa. 

#### The Knapsack Problem
Given the number of items `N`, the items themselves, `items[]`, which is an array of tuples `(weight, value)`, and `W`, the maximum weight capacity of the knapsack. 
This is a maximum problem: 

Let's look at the decision for each item: 
We either choose to have it or not. 
Then we think about the recursive element of this: where `i` represents the current item that we are on (considering from 0 to `N - 1`). 
`W` will have to be the remaining capacity of the knapsack at this stage. 
The base case here will have to be when we have considered all items, or if `w == 0`. 
The general idea is that we have to look at all possible combinations. 

```
def knapsack(i, W): 
	if i == N or W == 0: 
		return 0;
	if weight[i] > W: 
		return knapsack(i + 1, W);

	return max(knapsack(i + 1, W), value[i] + knapsack(i + 1, W - weight[i]));
```


#### Minimum Coin Change
Given a target amount `T`, and few coins, find the `minimum` number of coins needed to make up the target amount. 
Requiring the minimum (problem): 
We let recursion go through every possible avenue that we can make up here: 
I would say that the best way to do this to see the decisions, on the coin we are on is we choose the same coin again, or we move to the right in the list. 
If we are over T, then that's bad and we can discount that set of coins. 

#### Minimum Path Sum
Given a 2D grid of ints, where each num, represents the cost, find the minimum path sum from the top left to the bottom right corner. 

You can only move right or down at each step. 
As there are loads of paths that we could take, we should probably recurse over them all. 
If we are at the bottom, then we must go right, if we are at the right, then we must go down. 
This means that there will be a overlapping of some paths. 
We would have to pass away two parameters, the `i` and `j` that are relevant to the row and the column that we are in, then we are returning the path with the minimum. 
In terms of thinking about the subproblem here: 
I was so close to solving that immediately, which was fucking dope: 
I really like the idea of having a wrapper as well. 
At every number there is the rough possibility that we can go right, or we can go down: however, if we are at the edge, or beyond it, then we cannot go that way anymore.
```
template <typename T> 
int getMinMain(const T& grid, int i, int j, int w, int h)
{ 
	if(i >= w || j >= h)
		return std::numeric_limits<int>::max();

	if(i == w - 1 && j == h - 1)
		return grid[j][i];


	int right = getMinMain(grid, i + 1, j, w , h);
	int down = getMinMain(grid, i, j + 1, w , h);
	return std::min(right, down) + grid[j][i];
}

// then the wrapper, to set everything off
template <typename T> 
int getMin(const T& grid)
{ 
	int h = grid.size();
	int w = gird[0].size();
	return getMinMain(grid, 0, 0, w, h);
}
```

Then the memoization will just be creating another 2d grid, that will hold all of the min, remember that there are multiple ways to get to most points within the map, there are more ways the further out from the edges that we get to and the further down and right we go, but imagine a massive map, there are at least 3 ways to getting to most points. 

The thing with memoization is that if we already have the figure for that `i, j` then we just return that with `return memo[j][i]`. 

```
#include <vector>
#include <limits>
#include <unordered_map>

// Memoization helper function (using unordered_map)
template<typename T>
int returnMinimumPathMemo(const T& grid, int i, int j, std::vector<std::vector<int>>& memo) {
    int h = grid.size();
    int w = grid[0].size();

    // If out of bounds, return a high cost to prevent choosing this path
    if (i >= w || j >= h)
        return std::numeric_limits<int>::max();

    // Base case: if we reach the bottom-right cell, return its value
    if (i == w - 1 && j == h - 1)
        return grid[j][i];

    // If we've already computed this cell, return the stored result
    if (memo[j][i] != -1)
        return memo[j][i];

    // Recursive call: choose the minimum path moving right or down, adding the current cell's value
    int right = returnMinimumPathMemo(grid, i + 1, j, memo);
    int down = returnMinimumPathMemo(grid, i, j + 1, memo);
    memo[j][i] = std::min(right, down) + grid[j][i];

    return memo[j][i];
}

// Main wrapper function
template<typename T>
int returnMinimumPath(const T& grid) {
    // Initialize memoization table with -1 to indicate uncomputed cells
    std::vector<std::vector<int>> memo(grid.size(), std::vector<int>(grid[0].size(), -1));
    return returnMinimumPathMemo(grid, 0, 0, memo);
}
```

The key to these is that we look at the problem, and try and find the smaller problems that can help us solve the larger one. 
Remember that in recursion, we have the rules system, base case, and the recurrence cases. 
When we do the recursion, and we do the top down method: remember that bottom up, the iterative version, does not use recursion. 
Whereas, top down, will be using recursion. 
Very large scale problems will really need iterative, as we don't want to destroy the stack with our recursive calls. 

### House Robber 
Cannot rob two adjacent houses. Given an array of numbers, representing what you will earn from that house. 

The idea with this one is that at each house, we look at the decisions at each house, where we can either rob it or skip it. 

The base case, being that we are that the last house I believe: then the recursive cases are whether we decide to rob this house or move to the next one. 

```
template <typename T>
int bottomUp(T const& houses)
{  
    if(houses.empty()) return 0;
    if(houses.size() == 1) return houses[0];

    int n = houses.size();

    std::vector<int> dp(n, 0);
	
    dp[n - 1] = houses[n - 1]; // only choice is to rob the last house
    dp[n - 2] = std::max(houses[n - 2], houses[n - 1]); // either rob the last house or the one before

    for(int i = n - 3; i >= 0; --i)
    {
        dp[i] = std::max(houses[i] + dp[i + 2], dp[i + 1]);
        // the max of the current house plus one adjacent away
        // or with the house directly next to it
    }
	return dp[0];
}
```
Here we are doing the bottom UP approach, the idea is that at every single house, we have two decisions to make. 
Therefore, we either decide to rob this house, given that we have robbed the one adjacently before it, or we take the money from the house directly before it. 
The recursive version makes a lot more sense 
```
template <typename T> 
int robHelper(int i, T const& houses, T const& memo)
{ 
	if(i >= houses.size())
	{ 
		return 0;
	}
	// if already calced then return stored result
	if(memo[i] != -1)
		return memo[i];

	// at any point
	int robCurrent = houses[i] + robHelper(i + 2, hosues, memo);
	// at any point, we can also skip
	int skipCurrent = robHelper(i + 1, houses, memo);

	memo[i] = st::max(robCurrent, skipCurrent);
	return memo[i];
}
```
 Then as well, think about trying to lower the memory that we are using as much as we can: 
 A great solution from leetcode: 
 ```
 int rob(vector<int>& nums)
 { 
 // this time we are going forward, not backwards
	 if(nums.size() == 1) return nums[0];
	 int r = nums[0], p = max(nums[1], nums[0]); // same as ours
	 for(int i = 2; i<nums.size(); ++i)
	 { 
		 r = max(p , r + nums[i]);
		 swap(p, r);
	 } 
	 return p;
 }
```


#### Tower of Hanoi
This is a famous one. 

We look at if we solve at n - 1, for every n, until `n == 1` then that's just a trivial move. 

```
void towerOfHanoi(int n, char, source, char destination, char auxiliary)
{ 
	// base case; if only one disk left, just move it to the destination
	if(n == 1)
	{ 
		cout << "move disk 1 from" << source << " to " << destination << endl;
		return;
	}

	// move n - 1 disks from source to auxiliary peg
	towerOfHanoi(n -1, source, auxiliary, destination);
	
	cout << "Move disk" << n << " from " << source << " to " << destination << endl;

	// move the n - 1 disks from auxiliary to destination peg. 
	towerOfHanoi(n - 1, auxiliary, destination, source)
}
```

From the book:  this is in python
```
def solve(numOfDisks, startTower, endTower, tempTower)
{ 
	if numOfDisks == 1: 
		# BASE CASE
		moveOneDisk(startTower, endTower)
		return
	else: 
		solve(numOfDisks - 1, startTower, tempTower, endTower)	
		moveOneDisk(startTower, endTower)
		solve(numOfDisks - 1, tempTower, endTower, startTower)
		return
}
```
Let's start when we have 2 disks: 
The key here is remembering that each function will have its own variables, which may mix up start and end towers as we go along, the towers somewhat move as well.
`numOfDisks != 1` therefore we are in the else clause: and we have the first solve, which takes us to n - 1, where we are moving the first disk to the temp tower (which is known as the `endTower` in this specific function, it gets swapped as we move through the function): 
Then we are moving the second disk with `moveOneDisk` from the actual start to the end tower, whichever you want that to be. 
Then we are going to move the first disk again, from the temporary tower, to the end tower. 
That solves it for two disks. 
**With Three Disks**: 
First we move into the first solve with 2 disks, where temp tower is our end tower: 
Then we move to the one disk solution, where our end tower is actually our end tower (double swapped), this means that the first disk will be put onto the end tower. 
Then backtrack, remember in this function, our temp tower is the end tower, therefore we move the second plate to the temp tower. 
Then we solve for one disk again where we take from temp tower, which is actually the end tower here, move it to the end tower, which is actually our temp tower. This means that the start tower is not used here, rather it has to be there to keep it quite literally in the loop. 
Then we move back to the third disk function: where all the towers are as they are: 
Therefore we move the third disk to the end tower (actually the end tower). 
Then we go back through with the solve, however, the start tower is now the temp tower, and end tower is the end tower and the temp tower is the start tower. 
We are just changing the descriptions of what those towers are. 
We move into the 2 disk solution with those tower changes: You can immediately see how this works.

That is something to take note in, you can swap the args as we go through the recursive calls, which is really cool and something I've not thought about ever: in C++, as long as they are the same type of course, which is something that could be done using metaprogramming:
```
using vi = std::vector<int>;
void addSwap(vi& v1, vi& v2, int i)
{ 
	if(i == 10) // base case
	{ 
		v1.push_back(i);
		return;
	}
	
	v1.push_back(i);
	addSwap(v2, v1, i + 1);
	return; // will just return on the way back
}
```
When those two are printed out, using `NNNN` as a separator, we get something very cool indeed. 
![[Pasted image 20241111155104.png]]

Of course this could be done like this: 
```
void addSwap(vi& v1, vi& v2, int i) // where i is now the limit
{ 
	for(int j = 0; j <= i; ++j)
	{ 
		v1.push_back(j);
		std::swap(v1, v2); // this could be super costly
	}
}
```
Depending on the swap, this can be quite costly, therefore, the recursive method is probably easier here: 
```
void addSwap(vi& v1, vi& v2, vi& getFrom)
{ 
	for(int i = 0; i < getFrom.size(); ++i)
	{ 
		if(i & 1)// if odd
			v1.push_back(getFrom[i]);
		else 
			v2.push_back(getFrom[i]);
	}
}
```

All of these possibilities. 

#### Power Sets
NOT PERMUTATIONS: 
For `n` sized vector of numbers, there are $2^n$ power sets, remember the empty set, which will be important in a second. 

There is the recursive version, where we just look at adding the number or not. 
The key here is that we are not returning anything, therefore, we just think about the variable that we currently have in the function, get to the end of the set that we are on, and add it, therefore it is saved, and we don't have to return anything. 
```
using vi = vector<int>;

template <typename T> 
using vc = vector<T>;

void powerSet(vi& nums, vc<vi>& colOfPerms, vi& nums, int index)
{ 
	if(index == nums.size())
	{ 
		colOfPerms.push_back(currPerm);
		return;
	}

	// as we are in this function, we are not returning anything
	// all we have to think about is what the recursion will look like in this function
	// therefore, we can add and send that one on its way
	// then when it comes back, we will be backtracking to whatever the set was like in this function, with no regard for what it was like down the road
	// then we recurse for what the set will be like if we did not add thsi number 
	currPerm.push_back(nums[index]);

	powerSet(nums, colOfPerms, currPerm, index + 1);

	currPerm.pop_back(); // backtracking in this current set

	powerSet(nums, colOfPerms, currPerm, index + 1);
}
```

Notice that the recursion calls are exactly the same lol. 

There is this cool idea where we are using bits, we look at every bit of 1, if it's 1, then we add, if it's not, then we don't add: 
```
vc<vi> powerSet(vi const& nums)
{ 
	int n = nums.size(); 
	int numSubSets = 1 << n; // easiest way to get 2 ^ n
	vc<vi> subsets;
	for(int i = 0; i < numSubsets; ++i) // for every subset
	{ 
		vi subset; // current one
		for(int j = 0; j < n; ++j)
		{ 
			if(i & (1 << j)) // move 1 over j times, for the jth bit in i
			{ 
				subset.push-back(nums[j]);
			}
		}
		subsets.push_back(subset);
	}
	return subsets;
}
```

Here we are looking at the fact that the number i, which goes through all the possible number of sets there are in a power set. 

Another approach that is breadth first: breadth first, because we are going to do `{1}, {2}, {3}` before `{1, 2}`. 
```
vc<vi> powerSet(vi const& nums)
{ 
	vc<vi> subsets = {{}}; // giving us an empty set, 
	// this means the subsets has a size of 1
	for(int num : nums)
	{ 
		int size = subsets.size();
		for(int j = 0; j < size; ++j)
		{ 
			vi newSubSet = subsets[i];
			newSubSet.push_back(num);
			subsets.push_back(newSubSet);
		}
	}
	return subsets;
}
```


#### Target Sum
```
bool targetSum(const std::vector<int>& arr, int n, int target)
{ 
	if(target == 0)
		return true;
		
	if(n == 0 || target < 0)
		return false;

	// recruseive case
	// dec 1: include the current element
	bool include = targetSum(arr, n - 1, target - arr[n - 1]);

	// dec 2: excluse the current element
	bool exclude = targetSum(arr, n - 1, target);

	return include || exclude;
}
```

#### Word Break
```
bool wordBreak(std::string const& s, const std::unordered_set<std::string>& wordDict, int start)
{ 
	// start is 0 at the beginning. 
	
	
	if(start == s.size(0)
		return true;

	for(int end = start + 1; end <= s.size(); ++end)
	{ 
		// woudl be faster if we were using std::string_view
		std::string prefix = s.substr(start, end - start); 
		if(wordDict.count(prefix) && wordBreak(s, wordDict, end))
			return true;
	}

	return false;
}
```

