#customizingoperations
A lot of the algorithms will use a variety of either `< or ==` in order to check elements within the container. 

There are also versions that let us supply our own operations into the arguments. 

The `sort` algorithm will obviously use the `<` operator. 
However we might want to sort with a different idea, or into a different order, defined by `<` or a seqeuence might have elements of a type that does not have a `<` (although we will define operators in classes later).
We need to override the default behaviour of `sort`. 

### Passing a Function to an Algorithm
Imagine if we wanted to reorder the words by length in the vector, not alphabetically, which is the standard order that `sort` would be in. 
This argument would take a third argument, which is a **predicate**; 
### Predicates
#predicates
It's an expression that can be called and that returns  value that can be used as a condition. 
In this context they are unary predicates (they have a single parameter) or binary predicates (taking two parameters). 
The algorithm will take and call the predicate on the elements in the input range. 

As a result, it must be possible to convert the element type to the parameter type of the predicate. 

When we look at `sort`, we must have a predicate that will provide a consistent order for all possible elements in the input sequence. 

```
// comparison fucntion to be used to sort by word length
bool isShorter(const string& s1, const string& s2){ 
	return s1.size() < s2.size()
}
// sort on word length, shortest to longest
sort(words.begin(), words.end(), isShorter);
```

If we were really greedy, and wanted both, length and alphabetically, then we would have to use a `stable_sort`  algorithm. 

The general idea here is: why would we care for a sort that has two different requirements for the sort. After all, if we sorted once, based on a element of strings (eg. length, alphabetically etc.) then then why would we want more? 
However, in this case, with the length, the words are still alphabetically (and even if we sorted alphabetically, then they would still differ in lengths). 

```
elimDups(words); // put words into alphabetical order and remove duplicates
// resort by length, maintaining alphabetical order among words of the same length
stable_sort(words.begin(), words.end(), isShorter);
for(const auto &s : words)// no need to copy strings
	cout << s << "  :  ";
cout << endl;
```
Assuming that our words vector was in fact in alphabetical order before this call, after the call, `words` will be sorted by element size, and the words of each length remain in alphabetical order. 
When we run this code then we will have, 
`fox red the over slow jumps quick turtle`.


If we reversed the `isShorter`
```
bool isShorter(const string& s1, const string& s2){ 
	return s1.size() > s2.size();
}
```

[CPPREF Stable_Sort](https://en.cppreference.com/w/cpp/algorithm/stable_sort)
Sorts the elements in the range `[`first`,` last`)` in non-descending order. The order of equivalent elements is guaranteed to be preserved.


```
struct Employee
{ 
	int age; 
	string name; // does not participate in comparisons
};

bool Operator<(const Employee& lhs, const Employee& rhs)
{ 
	return lhs.age < rhs.age;
}

int main(){ 
	vector<Employee> v{{108, "Zaphod"}, {32, "Arthur"}, {108, "Ford"}};

	stable_sort(v.begin(), v.end());

	for(const Employee& e : v){ 
		cout << e.age << ", " << e.name << "\n";
	}
}
```

