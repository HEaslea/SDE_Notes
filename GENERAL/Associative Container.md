#associativecontainer
[[Sequential Containers]] #sequentialcontainers 

The sequential and associate will differ from each other in a fundamental way. 
Elements in an associative container are stored and retrieved by a key, compared to one by one, it's like a free pass for random access. 

They will differ in the ways that we can use the keys.

#map #set

The two most basic types are `map` and `set`. 

In `map`: they have key-value pairs. The key will serve as the index, and the value represents the data associated with the key. 

In `set`: contains only a key; a `set` supports efficient queries as to whether a given key is present. 
We might use a set to hold words that we want to ignore during some kind of text processing. 
A dictionary would be a good use for a `map`: The word being the key and the definition would be the value. 

The library will provide us with eight associative containers; They differ from each other in three dimensions; each container is a `set` or a `map`: they require unique keys or allows multiple keys, : they store elements in order or not. 
![[Pasted image 20240327011523.png]]

The containers that contain multiple keys include the word `multi`; 
Those that do not keep their keys ordered start with the word unordered; 
Hence an `unordered_multi_set` is a set that allows multiple keys of the same type (weird) whose elemenst are not in order. 
Whereas a `set` will have unique keys that are stored in order (more like in maths).

The unordered containers use a hash function to organize their element. 
We'll have more to say about that hash function later (not too much later etc.).

The `map` and `multimap` types are defined in the `map` header; the `set` and `multiset` are in the `set` header; and the unordered containers are in the `unordered_map` and `unordered_set` headers. 


### Using Associative Containers
#usingassociativecontainers
A `map` is a collection of key-value pairs. 
We might say that we are "mapping names to phone numbers" where names are the key and phone numbers are the values etc. 
Most often it's referred to as an "**associative array**". 
The subscripts in this case, don't have to be integers. Values in a `map` are found by a key rather than by their position. 

Essentially using non ints in order to subscript. 

A `set` is merely a collection of keys. 
This is most useful when we want to know whether a value is present, as we can subscript to that key, so we would know that it does exist in that collection of data. 
A business might define a `set` named `bad_checks` to hold the names of individuals who have written bad checks. 
Then the business might check to see whether someone is in that data set, to check who's a naughty Nelly. 

### Using a `map`
#usingamap
Word counting program. 
```
// count the number of times each word occurs in the input 
map<string, size_t> word_count; // emptymap from string to size_t
string word;
while(cin >> word)
	++word_count[word]; // fetch and increment the counter for word
for(const auto& w : word_count) // for each element in map
	// print the results
	cout << w.first << " occurs " << w.second << ((w.second > 1)? "times" : "time") << endl;
```

Notice that we have to template both the `string` and `size_t`. 
When we subscript, we use a `string` as the subscript, and we will get back the `size_t` associated with that `string`.
The while loop reads the standard input one word at a time. 
If the word is not already in the map, the subscript operator actually creates a new one, whose key is the word that we have tried to subscript, and the default value will be 0 here. 

#pair
When we fetch an element from a `map`, we will get an object of type pair, if we for range it. 
Briefly, a pair is a template type that holds two (`public`) data elements named `first` and `second`. 

The general: 
```
map<string, size_t> mm; 
string word; 
while(cin >> word)++mm[word];

for(const auto f : mm){ 
	cout << f.first << " : " << f.second << endl;
}
```
![[Pasted image 20240327013645.png]]
Notice how they are ordered. 

### Using a `set`
#usingset

The logical extension here is to ignore common words like "the", "and", and "or" and so on. 
We'll use a set to hold the words that we want to ignore and count only those that are not in set. 

```
// count the number of times each word occurs in the input
map<string, size_t> word_count; // empty map from string to size_t
set<string> exclude = {"The", "But", "And", "Or", "An", "A", "the", "but", "and", "or", "an", "a"};
string word;
while(cin >> word){ 
	// count only words that are not in exclude
	if(exclude.find(word)==exclude.end())
		++word_count[word];
}
```

### Overview of the Associative Containers
They all support the general container operations covered earlier. 
Associative container do not support the sequential-container position-specific operations, such as `push_front` or `back`.

As elements are stored based on their keys, these operations would be meaningless for the associative containers. 
Moreover, they do not support the constructor  or insert operations that take an element value and a count. 

In addition to sharing some of the operations from sequential containers, these containers provide some operations and type aliases that the sequential containers do not, of course. 

The unordered ones also provide operations for tuning their hash performance, which we'll cover later on. 

#### Defining an Associative Container
Each container will have the default constructor, creating an empty container using the templated type we pas sin. 
We can initialize as a copy of another of the same type or from a range of values, so long as those values can be converted to the type of that new container. 
```
map<string, size_t> word_count; // empty
// list initialization
set<string> exlude = {"the", "but", "and", "or", "an", "a", "The", "But", "And", "Or", "An", "A"};
// three elements; authors maps last name to first
map<string, string> authors = {{"Joyce", "James"}, {"Austen", "Jane"}, {"Dickens", "Charles"}};
```

Initializers must be convertible to the type in the container. 
For `set` the element type is the key type. 

When initializing a map, we have to supply both the key and the value of course. 
Have a look here and see that it is not initialized the same way that python dictionary is. 

Initializing a `map` needs to look something like this: 
`{key, value}`

#### Multimap and Multiset
#multimap #multiset
All the keys in a `map or set` must be unique: 
There can only be one element with a given key. 
However, these two sets, have no such restriction; there can be several elements with the same key. 
For example, the `map` we used to count words must have only one element per given word. 
On the other hand, a dictionary could have several definitions associated with a particular word. 

Illustrating the difference: 
First we'll create a vector of `ints` called `ivec` that has 20 elements: two copies of each of the integers from 0 through 9 inclusive: we'll use that `vector` to initialize a `set` and a `multiset`. 

We can use that `vector` to initialize a `set and multiset`. 
```
// defining a vector with 20 element , holding two cpies of each number from 0 to 9
vector<int> ivec;
for(vector<int>::size_type i = 0; i != 10; ++i){ 
	ivec.push_back(i);
	ivec.push_back(i); // duplicate copies of each number
}
// iset holds unique elements, as is the deifnition of a set; miset holds all 20 elements
set<int> iset(ivec.cbegin(), ivec.cend());
multiset<int> miset(ivec.cbegin(), ivec.cend());
cout << ivec.size() << endl; // prints 20
cout << iset.size() << endl; // prints 10
cout << miset.size() << endl; // prints 20
```

### Requirements on Key Type
Associative containers will place constraints on the type that is used as a key. For the ordered containers - `map, multimap, set, multiset` - the key type must define a way to compare the elements. 
By default, the library uses the < operator for the key type to compare the keys. 
In `set`, the key is the element type in map types the key is the first type. 

Callable objects passed to a sort algorithm must meet the same requirements as do the keys in an associative container. 

### Key Types for Ordered Containers
#strictweakordering
Just as we can provide our own comparison operation to an alg, we can also supply our own operation to use in place of the < operator on keys. 
The specified operation must define a **strict weak ordering** over the key type. 
We can think of a strict weak ordering as "less than". 
Although our function might use a more complicated procedure. 
However we define it, the comparison function must have the following properties: 
- Two keys cannot both be "less than" each other, if `k1` is "less than" `k2`, then `k2` must never be "less than" `k1`. 
- True of mathematical statements as well, if `k1 < k2, and k2 < k3, then k1 must be < k3`.
- If there are two keys, and neither key is < than the other, then we'll say those keys are "equivalent". If `k1 = k2, k2 = k3, then k1=k3`. This is where the weak idea comes into it, that items can be equivalent, if it were strict strong ordering, then things could not be == to each other. 

The strict idea comes into play in that there is a definitive way of ordering
This will mean that our container can see them as equivalent. 
When used as a key to a `map`, there will be only one element associated with those keys, and either key can beg used to access the corresponding value. 

### Using a Comparison Function for the Key Type
The type of operation that a container uses to organize its element is part of the type of that container. 
To use your own operation, we must supply the type of that operation when we define the type of an associative container. 
The operation type is specified following the element type inside the angle brackets that we use to say which type of container we are defining.
Each type in the angled brackets, is just that a type. 
So if we supply a class, we have to supply the idea that we can compare those objects with the strict weak ordering. 


We wouldn't be able to define a `multiset` of `Sales_data` because `Sales_data` doesn't have a `<` operator. 
We could use the `compareIsbn` function from the earlier exercises to define a `multiset`. 
That function will define a strict weak ordering based on their `ISBN`s of two given `Sales_data` objects. 

It should look something like this 
```
bool compareIsbn(const Sales_data &lhs, const Sales_data &rhs)
{ 
	return lhs.isbn() < rhs.isbn();
}
```

That function will define a strict weak ordering based on their `ISBN`
To use this definition, we must define `multiset` with two types; the key type, being `Sales_data`, and the comparison type, which is a function pointer, that can point to `compareIsbn`. 

Therefore we supply the pointer to the function that will supply the strict weak order. 

```
// bookstore can have several transactions with the same ISBN
// elements in bookstore will be in ISBN order
multiset<Sales_data, decltype(compareIsbn)*> bookstore(compareIsbn);
```
Using `decltype` in order to specify the type of our operation, remember that when we use this to form a function pointer, we need to add a `*` to indicxate that we're using a pointer to the given function. 
We initialize `bookstore` from `compareIsbn`, which means that when we add elements to `bookstore`, those elements will be ordered by their `ISBN` members. 
We can write `compareIsbn` instead of `&compareIsbn` as the constructor argument because when we use the name of a function, it is automatically decayed to a function pointer. 

So inside the <> we have the type that the set will contain, and then the type of comparison function it will use for sorting. 

Then the `bookstore(compareIsbn)`, is a constructor call for the `bookstore` object, initializing it with the provided function for comparison (strict weak ordering only for this type of set). It will use that function in order to compare and therefore sort all the elements. 
Just to note; that is why algorithms are so important, as they are constantly being used. 

[[The Pair Type]] #pair  #pairtype

### Operations on Associative Container
#operationsonassociativecontainer
Associative containers will also define these types too: 
![[Pasted image 20240329124249.png]]

For set types, the `key_type` and the `value_type` are the same; the values held in `set` are the keys too. 
In  a `map`, elements are `key-value` pairs. 
That is, each element is a `pair` object containing a key and an associated value. 
The key part of the `pair` is `const` as we cannot change the key. 

```
set<string>::value_type v1; // v1 is a string
set<string>::key_type v2; // v2 is a string
map<string, int>::value_type v3; // v3 is a pair<const string, int>
map<string, int>::key_type v4; // v4 is a string
map<string, int>::mapped_type v5; // v5 is an int
```
Here we are using the scope operator in order to get the type alias. 

Only `map` types have a `mapped_type`. 


### Associative Container Iterators
#value_type #map_type
Sets will make a little more sense here. 

When we dereference an iterator, we get a reference to a value of the container's `value_type`. 
When we look at a `map`, the `value_type`, is a pair, which the `first` will be a `const` key and the `second` holds the value: 
```
// get an iterator to an element in word_count
auto map_it = word_count.begin(); 
// *map_it is a refernce to a pair<strong string, size_t> object
cout << map_it->first; // prints the key for this element
cout << " " << map_it->second; // prints the value of the same element 
map_it->first = "New Key"; // error as map key values are const
++map_it->second; // fine as casn change value through iterator and 
```

**REMEMBER** that the `value_type` of a `map` is a pair and that we can change the value but not the key member of that pair, that is the biggest thing to think about here. 

### Iterators for `set` are `const`
#setiterators
#setimmutability

Although the `set` types define both the `iterator` and `const_iterators` types, both types of iterators give us read-only access to the elements in a `set`. Elements of a `set` are immutable. 
You can remember this, by thinking that the keys of a map are const, and everything in a set is a key, therefore, every element is `const`. 
Sets in that case are read only: Same as in maths, we can't just go adding to a set and expect everything to math mathematically correctly. 
```
set<int> iset = {0,1,2,3,4,5,6,7,8,9};
set<int>::iterator set_it = iset.begin();
if(set_it != iset.end()){ 
	*set_it = 42; // ERROR keys in a set are read-only
	cout << *set_it << endl;
}
```

### Iterating over an Associative Container
Both `map, set` provide all the `begin, end` operations that we have discussed before. 
We can rewrite the loop that printed the results in our word counting program earlier like this: 
```
// get an iterator positioned on the first element
auto map_it = word_coutn.cbegin();
// compare the current iterator to the off-the-end iterator
while(map_it != word_cout.cend()){ 
	// dereference the iterator to print the element key -- value pairs
	cout << map_it->first << " occurs"
		<< map_it->second << " times" << endl;
	++map_it; // increment the iterator to denote the next element
}
```

So here, we need to make sure that we understand that when we dereference these pointers, we get pairs, and then we need to access that pair's `first` and `second` members. 

Please Note that we are going to output them in alphabetical order, or whatever order the function we passed in that will order things, maybe it will output in order of word size etc. 

### Associative Containers and Algorithms
The fact the keys are `const` means that we cannot pass associative container iterators to algs that write to or reorder container elements, that cuts out a lot of the algs that we have, the associative containers will not need them anyways, part of the reason that we use them. 

Elements can be found very quickly using their key, it is almost always a bad idea to use a generic search algorithm. 
Associative containers define a member named `find`, which directly fetches the element with a given key, we could use a generic `find` algorithm to look for an element, but that algorithm does a sequential search, it sucks :). 

It is much faster to use `find` member defined by the container than to call the generic version. 

We might use the `copy` alg in order to copy from the associative container to another sequence. 
We can call `inserter` to bind an insert iterator to an associative container. 
Using `inserter`, we can use the associative container as a destination for another algorithm etc. 

### Adding Elements
#addingelementsassociativecontainers
The `insert` members add one element or a range of elements. 
`map` and `set` contain unique keys, inserting an element that is already present will have no effect: 
```
vector<int> ivec = {2,4,6,8,2,4,6,8}; // ivec has eight elements
set<int> set2; // empty
set2.insert(ivec.cbegin(), ivec.cend()); // set2 has four elements
set2.insert({1,3,5,7,1,3,5,7});
```
![[Pasted image 20240329152546.png]]

```
vector<string> names = {"Martin", "Cole", "Cole", "Vinnie", "Ashley"};
set<string> setNames;
auto iN = names.cbegin() + 2;
setNames.insert(iN, names.cend());
```
Output here is : `Ashley Cole Vinnie`. 

### Adding Elements to a Map
When we insert an element into a map, we have to remember that the element type is a pair, we can't just insert an int and hope etc. 
We don't need to create a pair object and then pass that in, we can create one in the insert arguments. 

```
// four ways of adding word to word_count (map)
word_count.insert({word, 1});
word_count.insert(make_pair(word, 1));
word_count.insert(pair<string, size_t>(word, 1)); // types explicit, must be matching
word_count.insert(map<string, size_t>::value_type(word, 1));
```
The last will construct an appropriate pair type. 

#### Testing the Return From Insert
The value return by `insert` depends on the container type. 
Containers with unique keys, the versions of `insert` and `emplace` that add a single element return a `pair` that lets us know whether the insertion happened. 
The `first` member of the pair is an iterator to the element with the given key; the `second` is a bool indicating whether that element was inserted, or was already there. 
If the key is already in the container, then `insert` does nothing, and the `bool` portion of the returned value is `false`. If `insert` was successful then the bool is `true` obviously.
```
// more verbose way to count number of times each word occurs in the input
map<string, size_t> word_count; // empty map from string to size_t
string word;
while(cin >> word){ 
	// inserts an element with key equal to word and value 1;
	// if word is already in word_count, insert does nothing
	auto ret = word_count.insert({word, 1});
	if(!ret.second) // if false, if already in word_count
		++ret.first->second; // increment the counter
}
```
The first type of the pair is the iterator to "possibly inserted" pair. 

For each `word` we attempt to put it in with a value of 1. 
If not in `word_count` then we add with the value of 1. 
The `if` looks at the `bool` part of the return value.
If it is `false` then the insertion didn't happen . 
Therefore we would have to increment, as the word is already in there. 

#### Unwinding the Syntax
The statement that is incrementing the counter in this version is hard to understand, kind of, the book thinks so.

`++ret.first->second`, the idea is that the pair that we get back from `insert` is a pair, the first element of which is an iterator of the possibly inserted element. 
The first is really just an iterator to if that key already exists in maps, then it will point to that as well. 
Then the -> will dereference this so we actually get that pair that we are iterating to. 
Then we have to use the second member of that. 

`++((*(ret.first)).second)` is the alternative, messy as anything,  not readable, yuck. 

We might write something like this: however, it is not necessary: 
```
pair<map<string, size_t>::iterator, bool> ret; // type of our return pair from insert

pair<map<string, size_t>::iterator, bool> ret =
	word_count.insert(make_pair(word, 1));
```

Remember that we define the type of iterator, that points to a container with certain types in it. 


### Adding Elements to `multiset` or `multimap`
The previous program will only occur in the fact that a given key can only occur once. 
Then there is only one counter associated with any given word. 
Sometimes, we want to be able to add additional elements with the same key. 
We might want to map authors to title of the books they have written. 
There might be multiple entries for each author, so we'd use a `multimap`, `insert` on these types always inserts an element: 
```
multimap<string, string> authors; 
// adds the first element with the key Barth, John 
authors.insert({"Barth, John", "Sod-Weed Factor"});
// ok adds the second element with the key Barth, John
authors.insert({"Barth, John", "Lost in the Funhouse"});
```
For the containers that allow multiple kyes, the `insert` operation that takes a single element return an iterator to the new element. 
There is no need to return a `bool`, because insert will always add a new element in these types. 

### Erasing Elements
These containers define three versions of erase, which are described in this table below: 
We can `erase` on element or a range of elements passing `erase` an iterator or iterator pair. 
These are similar to corresponding operations on a sequential containers: The indicated element(s) are removed and the function returns void. 
![[Pasted image 20240330132015.png]]
They provide an additional `erase` operation that takes a `key_type` argument. This version removes all the elements, if any,with the given key and returns a count of how many elements were removed. 
We can use this to remove a specific word from `word_count` before printing the results: 
```
// erase on a key return number of elements removed
if(word_count.erase(removal_word))
	cout << "Ok:" << removal_word << " removed\n";
else cout << "oops: " << removal_word << " not found\n";
```

For containers with unique keys, the return from `erase` is always either one or zero. If the return value is zero, then the element we wanted to erase was clearly no in the container. 

For types that allow multiple keys, the number of elements removed could be greater than one: 
`auto cnt = authors.erase("Barth, John");`

`cnt` will be 2 here. 

#### Subscripting a `map`
#mapsubscripting
Both `map` and `unordered map` containers provide the subscript operator and a corresponding `at` function, which are detailed below. 
The `set` types do not support subscripting because there is "value" associated with a key in a `set`, therefore what would it return? 
We cannot subscript a `multimap` or an `unordered_multimap` as there might be more than one value associated to the key that we input. 

![[Pasted image 20240330133907.png]]

Like other subscript operators that we have used, the `map` takes an index (that is , a key) and fetches the value associated with that key. 
However, unlike any other subscript operators, if the key is not already preset, a **new element is created and inserted** into the `map` for that key. 
If we were to write: 
```
map<string, size_t> word_count; // empty map 
// insert a value_initialized element with key Anna; thenm assign 1 to its value
word_count["Anna"] = 1;
```
Really really simple. 
```
#include <map>
int main(){
	map<string, int> Words; 
	Words.insert({"Hugo", 3});
	Words["Shakespeare"] = 10;
	for(auto elem : Words){ 
		cout << elem.first << "  " << elem.second << "\n";
	}
	cout << endl;
}
```

The following steps take place in their example (the first one): 
- `word_count` is searched for the element whose key is Anna. The element is not found. 
- A new key-value pair is inserted into `word_count`. The key is a `const string` holding `Anna`. The value is value initialized, meaning in this case that the value is 0. 
- The newly inserted element is fetched and is given value 1. 

Because the subscript operator might insert an element, we may use subscript only on a `map` that is not const. 
 Ordinarily, the type returned by subscript operators and dereferenced iterators are the same, however, not here. 
 When we subscript a `map`, we get a `mapped_type object;` when we dereference a `map` iterator, we get a `value_type` object. 
 
The `map` subscript operator returns an lvalue. As it is an lvalue, we can read or write the element. 
```
cout << word_count["Anna"]; // fetch the element indexed by Anna; prints one
++word_count["Anna"]; // fetch the element and add 1 to it
cout << word_count["Anna"]; // fetch element, prints 2
```
Unlike `vector, string` the type returned by `map` subscript operator differs from the type obtained by a dereferencing `map` iterator. 
If we wanted to know if an element is present, then using the subscript operator is a little strange. And we probably wouldn't use it for that clase: 

### Accessing Elements
#associativecontaineraccessingelements
These containers will provide multiple ways to find a given element, described below. 

![[Pasted image 20240330135537.png]]

If all we care about is whether a particular element is in the container, it is probably best to use `find`. 
For those that hold unique keys, it doesn't really matter if we use `find` or `count`. 
For containers with multiple keys, `count` has to do more work; if the element is present, it still has to count how many elements have the same key. If we don't need the count it's best to use `find`: 

```
set<int> iset = {0,1,2,3,4,5,6,7,8,9};
iset.find(1); // returns an iterator that referes to the element with key ==1
iset.find(11); // returns the iterator == iset.end();
iset.count(1); // returns 1
iset.count(11); // returns 0
```

### Using `find` instead of Subscript for `map`
#mapfind
For the `map` and `unordered_map` types, the subscript operator provides the simplest method of retrieving a value. 
However, as we've just seen, using a subscript has an important side effect: 
If that key is not already in the `map`, then subscript inserts an element with that key. 
Depending on our expectations, we might not want to add an element in order to check that an element exists. 

Sometimes we don't wanna change the map, but we want to check if the element does exist in the map. 
Therefore we need to use `find`: 
```
if(word_count.find("foobar") == word_count.end())
	cout << "foobar is not in the map" << endl;
```

#### Finding Elements in a `multimap` or `multiset`
Finding an element in an associative container that requires unique key is a simple-matter. 
The element is or is not in the container. 
With multimap and multiset, there are possibly multiple elements with the same given key, those elements are adjacent within the container. 
Using find and count is a very obvious way of doing it: 
```
string search_item("Alain de Botton"); // author we'll look for
auto entries = authors.count(search_item); // number of elements
auto iter = authors.find(search_item); // first entry for this author

// loop through the number of enetires there are for this author 
while(entries){ 
	cout << iter->second << endl; // print each title
	++iter;
	--entries; // keep track of how many we've printed
}
```
`entries` how many entries of that name there are. 
Getting the iterator of the first entry using `find`. 
If `entries = 0` at the beginning, then the loop will never execute. 
We are guaranteed that iterating across a `multimap` or `multiset` returns all; the elements with a given key in a sequence. 

### A Different, Iterator-Orientated Solution
Alternatively, wej can solve using `lower_bound` or `upper_bound`. 
Each of these operations take a key and returns an iterator. 
If the key is in the container, the iterator returned will be `lower_bound`, will refer to the first instance of that key and the iterator returned by `upper_bound`, will refer to the last element of that value. 
If the element is not in the `multimap`, then our `lower_bound == upper_bound`, it's important to note here that **both will point to the point at which the key could be inserted without disrupting the order**. 
Meaning, that calling `lower_bound` and `upper_bound` on the same key yields an iterator range. 
Of course, the iterator that may be returned here, it could be `off-the-end`. 
If the element we are looking for has the largest key in the container, then `upper_bound` on that key will return the `off-the-end` iterator. 
If the key is not present, but we are looking for a key that is larger than any others, then the return from `lower_bound` will also be the `off-the-end` iterator. 
The iterator from `lower_bound` may or may not refer to an element with the given key. If the key is not in the container, then `lower_bound` refers to the first pointer at which this key can be inserted while preserving the element order within the container. 
Using these new operations, we can do something like this: 

```
// definitions of authors and search_item as before
// beg and end denote the range of elements for this author
for(auto beg = authors.lower_bound(search_item),
	end = authors.upper_bound(search_item); beg != end; ++beg)
		cout << beg->second << endl; // print each title
```

This does accomplish the task a little more directly. The call to `lower_bounds` positions beg so that it refers to the first element matching the `search_item` if there is one (if not then it points to the place that we could place it without disrupting the order of the container). 
If there is no such element, then `beg` will refer to the first element with a key that is larger than `search_item`, which could be the off-the-end iterator. 
The call to `upper_bound` sets `end` to refer to the element just beyond the last element with the given key. 
These operations don't say anything about whether the key is present or not. 
The important thing to remember is that the return values act like an iterator range. 

We can check if the element is there manually by seeing if the two iterators are ==. 
They will both refer to the point that each key can be inserted while maintaining the order of the container. 

`beg` will refer to the first element in the container with the right `search_item`, if it does exist. 
Then we increment `beg` to move through the container with this key. 
When `beg == end`, we have seen every element with this key. 

The iterators form a range, we can use a `for` loop to traverse them. 
The loop is executed zero or more times and prints the entries that we traverse over. 
If no elements of the given `search_item` then the loop is never executed, it finishes at the conditional if the two iterators are the same. 
If `lower_bound` and `upper_bound` return the same iterator, then the given key is not in the container. 

### The `equal_range` Function
This is the most direct of the other approaches.
Instead of calling `upper and lower_bounds`, we can call `equal_range`. 
This function takes a key and returns a `pair` of iterators. 
If present, the first iterator refers to the first instance of the key and the second iterator refers one past the last instance of the key. 
Literally just a pair of the upper and lower bounds again. 
If no matching found, then both the first and the second iterators refer tot he position where this key can be inserted without ruining the order. 

```
// definitions as before 
// pos holds the iteratorts that denote the range of elements for this key
for(auto pos = authors.equal_range(search_item); pos.first != pos.second;
	++pos.first){ 
	cout << pos.first->second << endl; // printing the title
}
```
This is pretty much identical as the last one, with upper and lower bound. Instead of using local variables, `beg` and `end` are just part of the pair returned from `equal_range`. 




#### Coding Examples
```
vector<int> vi = {2,6,8,7,4,3,4,5,9,0,6,4,2,1,3,4,5,6,2,4,5,3,4};
set<int> si(vi.begin(), vi.end());
for(auto e : si){ 
	cout << e << " ";
}
cout << endl;
```
![[Pasted image 20240329132648.png]]



```
map<string, int> sMap;
string word;
while(cin >> word){ 
	if(word == "DONE"){ 
		printf("\nDone\n");
		break;
	}
	sMap[word] = word.size();
}

for(auto e : sMap){ 
	cout << ele.first << "  " << ele.second << '\n';
}
cout << endl;
```
![[Pasted image 20240329143054.png]]

```
bool SizeCompare(const string& lhs, const string& rhs){
    return lhs.size() < rhs.size();
}

int main(){

	map<string, int, decltype(SizeCompare)*> sMap(SizeCompare);
	// all we are doing here is passing the comparison function into the constructor of the map
	// This could be a function poiunter or a functor overloaded `operator()`
    string word;
    while(cin >> word){
        if(word == "DONE"){
            printf("\nDONE\n");
            break;
        }
        sMap[word] = word.size();
    }

    for(auto ele : sMap){
        cout << ele.first << "  " << ele.second << '\n';
    }
    cout << endl;
```

The thing to note with both of these is that they are forming a map, not a multimap, meaning that if we have duplicates, they will not be added here. 

Now with the second , where we have inputted our own comparison, that is based on the size fo the words that we have passed in, if we pass two words that are 3 letters long, then they both WON'T be added. It will just add to the map **THE FIRST INSTANCE OF UNIQUE ELEMENTS THAT WE HAVE INPUT**. 
So if I input `ba` followed by `ab`. Then the only one that is added is `ba` it was the first instance. 

We could do it with a multimap, we have to change our usage a little bit: 
```
multimap<string, int, decltype(SizeCompare)*> sMMap(SizeCompare);
// .... 
while(cin >> word){ 
	if(word == "DONE"){ 
		printf("\nDone\n");
		break;
	}
	sMMap.insert({word, word.size()}); // adding a pair into the multimap
}
```
 Then we can have something like this: 
 ![[Pasted image 20240329145645.png]]

So the ordering is done and sorted, and duplicates are considered by the way that the maps and sets are ordered. 
If, in the compare function, I wrote something like this: 
`return lhs[1] < rhs[1];` Then the second letter will be the determining factor.

Any words that have the same second letter, after we have entered the first unique instance of that, will be discarded. 

In this case you could still do something like this with input: 
```
jake
jAke 
```
they will both be entered, as we take the base type of the < operator here which is char and A < a in this character set. 

[[CPP if x in alist]]



```
class Test{ 
	int value; 
	int id; 
	static size_t RefCount;

public: 
	Test(int i) : value(i) id(RefCount) { ++RefCount; }
	Test() : Test(0) {}

	int GetValue() const { return value; }

	int GetId() const { return id; }
};

size_t Test::RefCount = 1;

bool CTV(const Test& t1, const Test& t2){ 
	return t1.GetId() < t2.GetId();
}

bool CTI(const Test& t1, const Test& t2){ 
	return t1.GetValue() < t2.GetValue();
}

typdef bool (*fp)(const Test&, const Test&);

int main(){ 
	Test nt(2);
	Test nt2(3);
	Test nt3(1);

	fp forSetCompare = &CTI;
	
	set<Test, fp> nset(forSetCompare); 
	// into set template we push the pointer to the type of function that we will be using for the compare 
	// we initialiaze using the address for CTI
	// so we pass in the pointer to the type of function we will be using 
	// then we initialize using the address of the Function that we are using

	// instead of using forSetCompare, we could've just as easily used CTI, without having to assign it to something

	nset.insert(nt); nset.insert(nt2); nset.insert(nt3);

	for(auto& elem : nset){ 
		// output however you want
	}
	// in this we will be sorting by the id, but using CTV we can sort by the value of the Test subjects
}
```


```
bool compareEntered(const int& i1, const int& i2){ 
	return i1 < i2;
}

typedef bool(*FP)(const int&, const int&);

int main(){ 
	map<int, string, FP> mP(compareEntered);
	vector<string> values; 
	int index = 1;
	string word;
	while(cin >> word){ 
		if(find(values.begin(), values.end(), word) == values.end()){ 
			values.push_back(word);
			mP.insert(pair<int, string>(index), word);
		}
		++index;
	}
	for(auto elem : mP){ 
		cout << elem.first << "   " << elem.second << endl;
	}
}
```