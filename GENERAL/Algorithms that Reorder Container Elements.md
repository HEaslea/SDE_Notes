`sort` will obviously change the order of the elements, using the element type's `<` operator. 

#eliminatingduplicates
Eliminating the duplicated words, we will first sort the `vector` so that duplicated words appear adjacent to each other. 

Once sorted, we can user another library algorithm, named `unique` to reorder the vector so that the unique elements appear in the first part of the `vector` 
Remember that algorithms will not resize containers. 
Then we will use `erase` to actually remove the elements: 
```
void elimDups(vector<string> &words){ 
	// sort words alphabetically so we can find the duplicates
	sort(words.begin(), words.end()); 
	// unique reorders the input range so that each words appears once in the
	// front portion of the range and returns an iterator one past the unique range
	auto end_unique = unique(words.begin(), words.end());
	// erase uses a vector operation to remove the nonunique elements
	words.erase(end_unique, words.end());
}
```

Say that our sort version is now: `"fox jumps over quick red red slow the the turtle"`
Noticing that `red` and `the` appear twice.

### Using `unique`
#usingunique
The `unique` algorithm rearranges the input range to "eliminate" adjacent duplicated entries, and returns an iterator that denotes the end of the range of the unique values. 

Remember that our algorithms will not change the container, therefore, it will not remove the adjacent. 
Instead, it overwrites adjacent duplciates so that the unique elements appear at the front of the sequence. 
The iterator return by `unique` denotes one past the last unique element. 
The elemenets beyond that point still exist, but we don't know what values they have. 

![[Pasted image 20240322233725.png]]

PLEASE JUST REMEMBER: that these functions will act on iterators, not on the containers themselves. 
ALGORITHMS CANNOT ADD OR REMOVE ELEMENTS. 


#### Using Container Operations to Remove Elements
In order to actually remove them, we need to use a container operation, which we do in the call to `erase`. 
We would arrange the range of elements from the one to which `end_unique` through the end of `words`. 
`words.erase(end_unique, words.end());`

You might call `unique` when there are no duplicate elements, then erase, that's fine because `end_unique` would just be the same as `words.end()` in the example as above. 

