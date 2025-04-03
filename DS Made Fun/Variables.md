Variables are memory address - Tag Index Offset 
Remember that RAM is separated out by bytes. 
So an `int` is 4 bytes. 
When using a variable - might be in cache
Remember that cache works on spatial and temporal locality - so it might be in the cache already. 

We have to check, using the tag part of the address. 

CPU has a **cache controller** to manage that process. Check if that data is in L1 L2 L3, by looking at the address tags. 

If found - `cache hit` - CPU can use it immediately
If not found - `cache miss` - Must be fetched from RAM, which takes a little while, then put into cache. 

When putting into cache - we put over the Least Recently Used. 

Variables are like the tops of folders and files, we don't have to remember how far in, or the "address" of that thing, but we casn isntantly access it. 


## Array
Homogenous Data Types - statically - size fixed at compile time. 

Contiguous memory of equal sized bins - due to homogenous data types in the array - that allows for Instant Access (RAM). 
Just by calculating their offset from the beginning, hence whey the first one is 0, the second element is 1: as in beginning + 1 (size of data type). 

`Location(item i) = Location(start of array) + Size of each element * i`. 

`std::array` or the built in one, really is just a wrapper around this contiguous block. 

We can't move over the entire bookshelf at once, we have to do it element by element. 

Then juggling values in order to swap, this is just like if we have two bookstores, then we need to swap the contents, you need to get the 1st one out, then move the 2nd in, then move the 1st to the 2nd, this will require some other storage to put the 1st one in originally. 

### Insertion Sort
Go through each element, then move that element down to the right point. 
This must use a lot of swaps. 
The items in bin 0 and i - 1, are all going to be sorted, they form a sub-array that is already sorted, then we move to the next element and find where it will be in the sorted array, and put it there. 
Storing coffee collection in order of freshness, earliest best-by-dates to the left side of the shelf: put a single bag at the front, then we get to the second, compare and see if it should go first or second. Then we get to the third bag, compare, and put it either first, second, or last (third).

Go bag by bag. 

![[Pasted image 20250202035617.png]]
Not the most efficient, however, it a good insight to how arrays work. 

## Strings
A wrapper around arrays of chars. 
```
bool stringEqual(std::string const& s1, std::string const& s2)
{
	if(s1.size() != s2.size())
		return false; 
	int N = s1.size();

	int i = 0; 
	while(i < N && s1[i] == s2[i])
	{ 
		i = i + 1
	}
	return 1 == N;
}
```