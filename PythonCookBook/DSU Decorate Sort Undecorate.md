AKA Schwartzian Transform

An Idea used for sorting complex objects efficiently and clearly. 

1. Decorate: Create a temporary list of tuples, where each tuple is `(key, item)`
2. Sort: Sort this list by the key
3. Undecorate: Extract the original items, now in sorted order. 

 When we want to order a list by some feature of the elements. 

```
words = ["banana", "pie", "Washington", "book"]

#decorate
decorated = [(len(word, word)) for word in words]

#sort 
decorated.sort()# sorts by the first element by default (length)

#undecorate
result = [word for (length, word) in decorated]
# now the words are  ordered by their length
```

Now this is mostly before Python2.4, as this was the only way to sort with custom key efficiently. 
Mostly replaced by the `key=` argument found in sort that we use with the iterable. 
`words.sort(key=len)`
To demonstrate what we learnt about arguments: 
```
def my_sort(lst, key=lambda x: x): #key is some function that will take the element and give an integer to be referenced as the key
	# NOW DSU - decorate sort undecorate
	decorated = [(key(item), item) for item in ls]
	
	#sort
	decorated.sort()
	
	#undecorate (replace origin list in - place)
	for i, (_, item) in enumerate(decorates): # enumerate gives us the index and the item
	# in this case the item is the key and the item
	# however, the key is no longer needed, therefore we can use the _ in order to denote
	# that it is there, however, it's values/ie. it will not be used.
		lst[i] = item
```

