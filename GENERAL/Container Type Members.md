#containertypemembers
[[Overview of Sequential Containers]]

Each container defines several types. 
We have used a couple of these already : `size_type` `iterator` and `const_iterator`.
In addition to the iterator types, most containers provide a reverse iterator system. Therefore using `++` on a reverse iterator will yield the previous element, then our loop can look almost the same. 
```
vector<int> vInt;
    for(int i = 0; i < 20; ++i){
        vInt.push_back(i);
    }
	
    auto rbeg = vInt.rbegin();
    auto rlast = vInt.rend();

    while(rbeg != rlast){
        cout << *rbeg << endl;
        ++rbeg;
    }
```

The auto type will be of `std::reverse_iterator<vector<int>::iterator>`
`std::reverse_iterator<vector<int>::iterator>`

The remaining type aliases, let us use the type of the elements stored in a container without knowing what that type is. 
If we need the element type, we refer to the container's `value_type`. 
If we need a reference to that type, we use `reference` or `const_reference`. 
These are most useful in generic programs, that we will cover late on. 

To use one of these types, we must name the class of which they are a member: 
```
// iter is the iterator type defined by list<string>
list<string>::iterator iter;
// count is the difference_type type defined by vector<int>
vector<int>::difference_type count;
```

These declarations use the scope operator to say that we want the `iterator` member of the `list<string>` class and the `difference_type` defined by `vector<int>`. This should make more sense, then making it more complicated, as we can see how the classes vary. 

You have to think about how to assign these: 
```
std::vector<int> vec = {1,2,3,4,5};
std::vector<int>::iterator it1 = vec.begin();
std::vector<int>::iterator it2 = vec.end();

std::vector<int>::difference_type diff = it2 - it1;
```

for my own code: 
```
typedef vector<int>::iterator vIt;
// 

int main(){ 
	vector<int> vInt;
	for(int i = 0; i < 20; ++i){ 
		vInt.push_bacK(i);
	}
	vector<int>::difference_type diff;
	vIt vBegin = vInt.begin();
	vIt vEnd = vInt.end()
	diff = vEnd - vBegin;
	cout << diff << endl; // will output 20, as in there are 20 elements in the iter range
}
```

### `begin` and `end` members
There is also the idea that an iterator might be const, therefore we cannot change the value that the `iter` refers to.

```
list<string> a = {"Milton", "Shakespeare", "Austen"};

auto i1 = a.begin(); // list<string>::iterator
auto i2 = a.rbegin(); // list<string>::reverse_iterator
auto i3 = a.cbegin(); // list<string>::const_iterator
auto i4 = a.crbegin(); // list<string>::const_reverse_iterator
```

The functions not beginning in c are actually overloaded. Meaning there are two members that are named `begin`. 
One is a `const` member that returns the container's `const_iterator`
The other is `nonconst` and returns the container's `iterator` type. 

Similarly for `rbegin, end, and rend`. 
When we call one of these members on a non`const` object, we get the version that returns `iterator`. 
We get a `const` version of the iterators only when we call these functions on `const` objects. 

As with pointers, and references, to `const`, we can convert a plain `iterator` to the corresponding `const_iterator` but not vice versa. So not `const` to the `nonconst`. 

The c versions were introduced by the new standard to support using `auto` with `begin` and `end`. 
In the past, there was no choice but to say which type of iterator we want: 
```
// type is explicitly specified
list<string>::iterator it5 = a.begin();
list<string>::const_iterator it6 = a.begin();
//iterator or const_iterator depending on a's type of a
auto it7 = a.begin(); // const_iteratotr only fi a is const
auto it8 = a.cbegin(); // always a const_iterator
```

The iterator type we get depends on the container type. 
How we intend to use the iterator is irrelevant. 
The c versions let us get a `const_iterator` regardless of the type of the container. 
When write access is not needed, we should be using `cbegin() and cend()`.
