```
std::set
std::map
std::multiset
std::multimap
```

## `std::set`
Ordered! 
Collection of unique elements, each element follows a strict order. 
NO TWO ELEMENTS ARE THE SAME - just like in mathematics. 

Automatically sorting elements as they are inserted - no duplicates
Great for frequent lookups and insertions. 

A Unique Collection - Imagine student IDs. 

Ordered Data - Imagine a leaderboard.

It has a tree based nature - self-balancing binary tree - usually a red-black tree. 
Insertions are `O(log n)` 
Self-balancing, that means that we are avoiding `O(n)`. 

Great for all the mathematical set ideas well (separate to bitsets). 

Event scheduling - keeping track of times for this. Given an ordered nature. 

If ordering not essential, then just use a `std::unordered_set`. 

Insertion - `O(log n)`
Deletion - `O(log n)`
Access - `O(log n)` 
Everything with the binary trees. 
Memory overhead higher than `std::vector` due to the balancing binary tree nature.

`std::multi_set` - allowing repeating elements - `std::set` doesn't allow that obviously. 

`unordered_set` actually has really good access as its a hash table (`O(1)` insert/find at the cost of no ordering).

Sorting is done on entry. 
Searching `O(log n)` with the `find` member function. 

`lower_bound` `upper_bound` 
NOT GREAT - for frequent random access or if order isn't a concern. 

```
std::set<int> nujmbers = {5, 3, 8, 1, 4};

auto [position, wasInserted] = numbers.insert(6); // gives us a pair

auto result = numbers.insert(5);
if(!result.second) { ... }

if(numbers.find(3) != numbers.end()) { ... } // it was found

numbers.erase(1);


std::set<int> moreNumbers ... 

numbers.merg(moreNumbers);

std::set<std::string, bool (*)(const std::string&, const std::string&)> 
caseInsensitiveSet{[](const std::string& lhs, 
	const std::string& rhs) 
	{ 
		return std::lexicographical_compare(lhs.begin(), lhs.end(), rhs.begin(), rhs.end(), [](char a, char b) 
		{ 
			return std::tolower(a) < std::tolower(b);
		});
	}};

```

Remember that access time is not constant - frequent access may not be what we want. 

YOU CANNOT CHANGE A VALUE ONCE ITS BEEN PUT IN THE SET. 
Everything in a set is immutable. 

`std::unordered_set` is still my king - due to the fast access time. 

leverage in place construction - emplace - avoids making a temporary object. 
Remember RVO and NRVO - returning a temp object from a function - returning a named local object (if optimizations are allowed). 

Remove original - introduce an altered counterpart - meaning that we can't change in `std::set` we have to remove then add. 

`find()` in order to find lol 

Strict Weak Ordering for comparators

## `std::map`
Is more about relationships, we have a key and a value. 
`[key, value]`

`RBT` - balanced binary tree. 
Log access - insert and deletion times. 
Sorted by key values. 

Unique keys - there can be no duplicated elements. 

Dictionary - that is what it is called in python 

Config keys - using this we can associate settings with config keys, easy retrieval modifications of settings. 

Frequency counter - this is a big one - huffman encoding structure. 

Inverted Index - keywords to a list of docs or locations where they may appear. 

Caching mechanisms - answers to expensive computations that can be done repetitively - just save them in there. `[question , answer]` or `[input, result]`

Same as set as the balanced binary tree is in full effect here as well: 
Insertion - `O(log n)`
Deletion - `O(log n)`
Access - `O(log n)` to locate a key 

Tree based structure - means a memory overhead more than the hash table ones. 

We can use `emplace`
`at`
`operator[]`
`lower_bound` `upper_bound`

```
std::map<std::string, int> ageMap = 
{ 
	{"Lisa", 25}, {"Corbin", 30}, {"Aaron", 22}
};

ageMap["Kristan"] = 28;
ageMap.insert_or_assign("Lisa", 26);

if(ageMap.find("Corbin") != ageMap.end()) { ... }

ageMap["Aaron"] += 1; // or ++

// structured bindings
for(const auto &[name, age] : ageMap) { ... }

ageMap.erase("Corbin");

if(ageMap.count("Regan") == 0) { ... } // regan does not exist in map

std::map<std::string, int, in 
	bool(*)(const std::string&, const std::string&)> 
	customerOrderMap{[] (const std::string& lhs, const std::string& rhs)
	{ 
		return lhs > rhs; // reverse lexicographic order
	}};

// Then A will be last
```

Similar to `std::set` we are given: The key remains constant for the lifetime of the element, it is immutable. 

If we need to update the key, we remove it and add the new one. 

`std::unordered_map` for faster access time. 

Safe key access - `operator[]` - is a double edged sword - it does not exist - it will insert. 
So we would need to use the `find()` method instead. 

`count` is a great way to see if we have a key there. 
`erase` will return an iterator - therefore we need to use that not to invalidate our used iterator which will happen. 

## `std::multiset`
Multiple elements to have the same value. 

Still ordered 
Allowing duplicates now
Offering log time complexity for insertion and deletion, and search, just slightly different implementation due to the values being the same. 

Random access is not a thing her. 

Ranking systems - Same ranking 

Better for statistics - we require the repeated numbers for accuracy. 

Event management - efficiently managing repetitions, if someone picks the same thing. 

Inventory management - representing what people are paying for. 

Document Term Frequency - text processing and term frequency - can hold multiple of the same word. 

```
std::multiset<std::string> wordMS;
std::string text = "This is a test this is only a test";
std::stringstream ss(text);
std::string word;
while(ss >> word)
{ 
	wordMS.insert(word);
}

std::map<std::string, int> wordCount; 
for(const auto& w : wordMS)
	wordCount[w]++;

for(const auto& [word, count] : wordCount)
{ 
	std::cout << word << " : " << count << "\n";
}
```

Spatial Data Structures - In computational geometry or graphics, sweep line algorithms use `std::multiset` to manage events or points efficiently, especially when multiple events share the same position. 

Handling collisions - this is a great container for them. 

Everything for this is `O(log n)`

Will use dynamic allocation so that nothing really has to grow. 
Allocators can influence node memory management. 

`std::unordered_multiset`, average constant time complexity for operations but does not maintain order. 

Sorting - always sorted

Searching - `O(log n)`

`equal_range` get the ranges of a particular element that is found. 

```
std::multiset<int> nums = {5, 3, 8, 5, 3, 9, 4};
nums.insert(6);
nums.insert(5);

for(int num : nums) { ... }
auto [begin, end] = nums.equal_range(5);

nums.erase(5);

std::multiset<std::string, std::greater<>> words = 
{ 
	"apple", "banana", "cherry", "apple"
};

std::multiset<int> dataset = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

const auto start = dataset.lower_bound(4);
const auto stop = dataset.upper_bound(7);

// never seen this one before
std::copy(star, stop, std::ostream_iterator<int>(std::cout, " "));
```

**Quickly on `std::ostream_iterator`**
That is an output iterator that writes elements to an output stream that we pass to it. 

`std::ostream_iterator<T> it(std::ostream& os, const char* delimiter = nullptr);`

If we do not need duplicates - then we should not be using this. 

With all the ordered ones - there is `O(log n)` in order to find and insert etc. 

This is extended to: 

## `std::multimap`
Storing key-value pairs. 
Multiple values with the same key. 
Elements are still found in an ordered manner. 

Maintaining collection order with non-unique keys. 
Sorted and accessed based on keys. 

When we have multiple entries per key - unlike a dictionary in this case. 
One key with several values. 

Multi-language dictionary : one word might be mapped to several others. 

Airport flight schedules - flights to the same destinations at various times. 

Event scheduling - a date or time might have several events or tasks. 

Graphical Representations : Computational structures - node in a graph connected to other nodes. `std::multimap` can be good here. 

Course en-rollment systems: a single course (key) can have many students or multiple instructors. 

This is the way to go when we have one-to-many relationships are prevalent. 
If sorting an ordering is not crucial, then we should be using `std::unordered_multimap`. 

Remember all of this is tree based: `O(log n)`. 

Please remember that all of this is logarithmic. 

The tree based underpinning. 

Harness the fact that it's sorted properly. 

Verify key access prior to access in that `[]` will just add that element in. 
`find` or `count` that could work. 

Remember that with multiple of the same value: `equal_range`, provides a range of all elements with that key, which is crucial to access them all. 

Can be inefficient with large datasets due to the tree nature. 

