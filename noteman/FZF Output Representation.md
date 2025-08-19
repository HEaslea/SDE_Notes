The idea here is that we are going to have a bunch of file names, and we need each one to have a path associated with it. You could just have a dictionary that has the names of the file as the key and then the path as the value, however, we would have a lot of repeated values. 

Therefore, we reverse it and think about another data structure. Where we only represent the path once in memory and have a bunch of files associated with that path, which is exactly the way that dirs work, so it's mimic that. 

My first thought, intuitively is to have an `n-ary` tree, where the parent nodes represent the path, and then the children all just contain the file name. 
This is half correct, in that, it doesn't paint the full picture. 

### Actual Directory Tree (trie over components)
A `trie` is a prefix tree, used mainly to store strings, where each edge represents a character (or element), and common prefixes are shared, between the children.
```
(root)
 ├─ c ─ a ─ t
 │       └─ r
 └─ d ─ o ─ g
```

Should be clear just on that alone. 

Each node here will represent part of a key (in this case, another dir in the path). 
The key question for our implementation is whether we add dirs that have no files whatsoever, I think so, just in case. 
If we do, there is no issue and lapse in performance. 

There will be `n` children, where there are `n - f` paths in a dir into other dirs, and `f` files. 
`f` will be leaf nodes. 

This will require more overhead, more pointers/maps at each node. 
More overhead per entry, a node per entry. 
Or you could put all the fil names in a `vector` node. 

This is very very useful when we want to say, give me all the entries under `/usr/local` for instance. 

This is an implementation that I found that isn't exactly it, however, I will also write a small implementation afterwards as well: 

```
struct DirNode { 
	// child dir name -> node 
	std::unordered_map<std::string, std::unique_ptr<DirNode>> dirs;
	
	// files just directly in this vector
	std::vector<std::string> files;
};

struct PathTree { 
	DirNode root; 

	static void normalize(std::filesystem::path& p)
	{ 
		p = p.lexically_normal(); // see below
	}

	void insert_file(const std::filesystem::path& full) // take in an temporary
	{ 
		
		std::filesystem::path = full; // seems redundant but i will take their word for it
		// i guess making an lvalue out to the temp that we got in. 
		
		normalize(p);

		// the usual tree algorithm thinking here
		DirNode* cur = &root;
		for(auto& part : p.parent_path())  // i did not know that you could do this tbf
		{ 
			auto key = part.string(); 
			auto it = cur->dirs.find(key);
			if(it == cur->dirs.end())
				it = cur->dirs.emplace(key, std::make_unique<DirNode>()).first;
			cur = it-> second.get();
		}
		cur->files.push_back(p.filename().string());
	}

	// find a dir node by path : basic searxch through tree
	DirNode* find_dir(const std::filesystem::path& dir)
	{ 
		std::filesystem::path p = dir; // turn into a path lvalue here
		normalize(p); // turn it into a  usable name 
		DirNode* cur = &root;
		
		for(auto& part : p) // iterate through
		{ 
			auto it = cur->dirs.find(part.string());
			if(it == cur->dirs.end()) return nullptr;
			cur = it->second.get();
		}
		return cur; // then just return the pointer to the node
	}
};
```


The only thing that we could do, if files are unique, which they have to be, is that we put the file names in `std::unordered_set`. 

Having them in a set will also enforce uniqueness. 
If we are looking for a file, then we just use a unique function in order to get the path, which is the parent node `_path` + the name of the file. 

Remember, however, that a vector will store things contiguously, meaning that we have better cache locality. 
If there are really only 3-20 files, the linear search will be slightly better. 

There is no hashing or tree overload. 

Vectors can also maintain an idea of order.

`set` will force a strict ordering, `unordered_set` gives an arbitrary order. 

The thing is that if we have a huge directory then we should be using an `unordered_set`, however, if there is only a few files, then we could use a `vector`. 

### Compact Path-interned
Storing one row per path : `{id, parent_id, name, is_dir}`. 

```
struct NodeRow{ 
	uint32_t id, parent; // parent == 0 for root 
	std::string name;    // For the file
	bool is_dir;         // Pretty obvious
};

struct PathIndex{
	std::vector<NodeRow> rows;      //  id -> row (id = index)

	std::unordered_map<std::string, uint32_t> dir_intern; // full path -> id 

	uint32_t ensure_dir(const std::filesystem::path& dir)
	{ 
		auto key = dir.string(); // get the string for the path that we passed in 
		
		// check if we find it, if the file exists
		if(auto it = dir_intern.find(key); it != dir_intern.end()) return it->second;
	
		uint32_t parent = 0; 
		auto parent_path = dir.parent_path();
		if(!parent_path.empty()) parent = ensure_dir(parent_path); // some recursion

		uint32_t id = (uint32_t)rows.size(); 
		rows.push_back({id, parent, dir.filename().string(), true});
		dir_inter.emplace(std::move(key), id);
		return id;
	}

	void insert_file(const std::filesystem::path& full)
	{ 
		auto dir = full.parent_path();	
		uint32_t d = ensure_dir(dir);
		uitn32_t id = (uint32_t)rows.size();
		rows.push_back({id, d, full.filename().string(), false});
	}
};
```

[Lexically_normal](https://en.cppreference.com/w/cpp/filesystem/path/lexically_normal)


### Some of the DS's we used here: 

1. `Trie` - Storing sequences
2. `n-ary` tree - general tree where each node can have multiple children (dir hiearchies). 
3. `Parent-refrence table` - `Adjancency List / Flat Tree Representation` -  store each node with `{id, parent_id}`, common in databases and filesystems.
4. `Disjoint set (union-find/ DSU)` - parent pointer structure for partitioning elements into groups, not hierarchical but useful for connectivity problems (not very useful here as far as I can tell). 

#### Hierarchical Data in Relational Databases 
This is pretty much what a file system is. 
###### The Adjacency List 
We give each `thing` an `id` - then we have a field for `parent_id` 

![[Pasted image 20250818000948.png]]

This relates to the `MySQL` Adj List Model. 

[MySQL Tutorial](https://www.mysqltutorial.org/mysql-basics/mysql-adjacency-list-tree/)

![[Pasted image 20250818001327.png]]

Clearly, giving everything an id, just gives us the full path right there and then. 

The `is_dir` one is a little weird, as you would think that there is a way to tell, based on there being no children on that row, however, you go only go up here, you can't say, hey, go to the child of this one, which is what you can do in a tree, therefore, like a reverse tree. 

Always think about which way the links are going. 

Two other techniques: 1. Nested Sets 2. Materialized Paths

**Nested Sets**: Each node encodes its descendants using two numbers. 
1. Left : A node's left number is less than all the numbers used by the node's descendant.
2. Right : A node's right number is more than all the numbers used by the node's descendant. 

Meaning that the indexes are numbered starting at the root of the tree and working from left to right through each node of the tree. 

Tree traversal is a big thing here: 
[Nested Set Model](https://en.wikipedia.org/wiki/Nested_set_model)

This is to number the nodes according to a tree traversal, which visits each node twice, assigning numbers in the order of visiting, and at both visits. 
Giving us two numbers for each node, they are stored as two attributes, therefore we have a relationship between the two. 
The relationship between the two gives an idea about the hierarchy membership. 

![[Pasted image 20250818005914.png]]
We can look at the difference of the two numbers. 
The greater the difference, the more children/descendants that node has. 
A leaf node (child) that is at the end, will only have a difference of one. 

`left` & `right` - they are assigned in a DFS manner. 
When we first visit, give it `left` and when we get back to it `post-process` we put the `right` into it. 

**Why?** : The point is all about query efficiency for hierarchy: 
In order to get all the descendants of `X` = just a range check: 
`WHERE left BETWEEN X.left AND X.right` and there is no recursion (bar giving the nodes their numbers). 
Updates are clearly expensive, we have to redo the nodes, shifting `left/right` values as we go. 

![[Pasted image 20250818010908.png]]![[Pasted image 20250818010956.png]]
Each node, in essence is a container holding the indexes of each of the child nodes below. 
The `left` of the child is going to be one more than the parent, and the `right` is going to be one less. 

Therefore to query everything, we just need to ask which nodes have a `left` value between `left` and `right` of the node that we are looking at. 

[GO HERE](https://medium.com/%40rishabhdevmanu/from-trees-to-tables-storing-hierarchical-data-in-relational-databases-a5e5e6e1bd64) and Materialized Path. 


