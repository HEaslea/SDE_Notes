AKA **Disjoint Set Union** (DSU), merge find set. 
Disjoint (non-overlapping) sets. 
There are a few ways of representing and implementing this idea. 
They are often implemented using **disjoint-set forest**. 

Efficiently manage a collection of disjoint sets. 
Supporting two main operations: 
1. Find
2. Union : merge two sets into one. 

**Forest**: is the idea of having one or more domain trees that do not form a contiguous namespace. 
All trees are forests, but not all forests are trees. 
Overall a collection of disjoint trees. Each tree in a forest is a connected acyclic graph, a forest may be a bunch of disconnected trees. They are used to represent a bunch of sets of data, that will form a tree structure, for each, such as a Union Find Data Structure. 

**Really we a constructing a FOREST**. 

The idea is that intersection of sum data sets is going to be a null set. 

Making a new set: 
```
function MakeSet(x) is
	if x is not already in the forest then 
		x.parent := x
		x.size := 1
		x.rank := 0
	end if 
end function	
```

```
class UnionFind
{ 
private: 
	std::vector<int> parent; // Store parent of each node
	std::vector<int> rank; // store the rank (tree height) of each node
	
public: 
	UnionFind(int n) : parent(n), rank(n, 0)
	{ 
		for(int i = 0; i < n; ++i)
		{ 
			parent[i] = i; // each element is its own parent
		}
	}

	int find(int x)
	{ 
		if(parent[x] != x) 
		{ 
			parent[x] = find(parent[x]); // path compression
		}
		return parent[x];
	}

	void unite(int x, int y)
	{ 
		int rootX = find(x);
		int rootY = find(y);
		if(rootX != rootY)
		{ 
			if(rank[rootX] > rank[rootY])
			{ 
				parent[rootY] = rootX;
			} else if( rank[rootX] < rank[rootY])
			{ 
				parent[rootX] = rootY;
			} else 
			{ 
				parent[rootY] = rootX;
				rank[rootX]++;
			}
		}
	}

	bool connected(int x, int y)
	{ 
		return find(x) = find(y);
	}
};
```

ELI5: 
There are 10 kids in the playground, each kid starts of standing by themselves. 
They are all wearing name tags with their own names. 
Then the kids start to form groups. 
Every group will have a leader, transformed from the norm by the nuclear group. 
When you ask a kid, who is the leader, the kid either knows or points to that person. Eventually someone will point to the leader of the group, starting from anyone in the group, we will eventually get to the leader. 
If a kid points directly to the leader, it's quicker next time. This is called **path compression**. 
If they want to join groups, the smaller group joins under the bigger group's leader so the chain doesn't get too long. This would be **union by rank**. 


```
int find(int x) // getting the leader of the grup that x belongs to
{ 
	if(parent[x] != x) 
	{ 
		parent[x] = find(parent[x]); // if not leader of themselves
	}
	return parent[x];
}
```
The idea is that we are following the chain back to the parent. 

Then uniting: 
```
void unite(int x, int y)
{ 
	int rootX = find(x); // get the parent of x
	int rootY = find(y); // get the parent of y

	if(rootX != root Y) // if they don't have the same parents
	{ 
		if(rank[rootX] > rank[rootY]) // if size of x group is > y group
		{ 
			parent[rootY] = rootX;
		} else if (rank[rootX] < rank[rootY]) // if x group is smaller
		{ 
			parent[rootX] = rootY;
		} else  // here i believe they are equal
		{ 
			parent[rootY] = rootX; 
			rank[rootX]++;
		}
	}
}
```

If they are connected: 
```
bool connected(int x, int y)
{ 
	return find(x) == find(y);
}
```

From wikipedia: 

```
function Find(x) is 
	if x.parent != x then 
		x.parent := Find(x.parent)
		return x.parent
	else 
		return x
	end if 
end function
```
or 
```
function Find(x) is 
	root := x // remember that at the leader will be its own leader
	while root.parent != root do  // while not at the leader
		root := root.parent // we move up
	end while // then when we are there, at the leader

	while x.parent != root do // 
		parent := x.parent
		x.parent := root
		x := parent
	end while
	
	return root
end function
```

MakeSet is the idea that each number will be its own leader and they are not a part of any groups: 

Then when we are merging (union) then here we are going to do that by size, so the smaller joins into the larger one. 
First, we need to find the roots of the trees. 
If the two roots are the same, then there is nothing more to do, however, if they are not, then we need to do more. 
In order to merge, the pointer of x's root to y's, or vice versa. 
The way in which they are added together does have consequences. 
We don't want the tree to become `O(n)` when we come to `Find`. 
As that just goes up the branch to the root node. Therefore we want to do **Union by Size**  or **Union by Rank**. 
Therefore, we need a node to store a pointer that's more than just its parent pointer. 
The idea is that the smaller gets put into the larger: 
```
function Union(x, y) is 
	// replace nodes by roots
	x := Find(x) // get the parent of x
	y := Find(y) // get the parent of y 

	if x = y then 
		return // x and y are in the same set already
	end if

	if x.size < y.size then 
		(x , y) := (y, x)
	end if

	 // make x the new root
	 y.parent := x
	 x.size := x.size + y.size
end function
```

**Path Compression** : This means that we can have all the nodes in the tree move point directly to the parent. 
The way to do that is: 
```
int find(int x)
{ 
    // implicit base that if parent[x] == x, then we just return that number
	if(parent[x] != x) 
	{ 
		parent[x] = find(parent[x]); // this means that every node along the path
		// will then point to x
	}
	return parent[x]; 
	// this will flatten out the tree
}
```

2 birds with one stone. 


#### Minimum Spanning Tree

![[Pasted image 20241210010914.png]]

The idea is that in a graph with weighted edges, we find the tree that links all the nodes in some fashion such as that. It's the subset of edges of a connected, edge-weighted undirect graph that connects all the vertices together, without any cycles and with the minimum possible total edge weight. 
Imagine it like a telecommunications trying to lay new cable throughout a neighbourhood. 
There may be one possible tree in a graph. 
