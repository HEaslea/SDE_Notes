The majority of the book is written in C, I will do my best so translate. 
### The SnowFlake problem: 
The idea here is that we have a bunch of snowflakes, which are arrays of 6 integers. 
How two snowflakes are the same if they can be orientated to be the same. 
```
These are all the same: 
1 2 3 4 5 6 
1 2 3 4 5 6
4 5 6 1 2 3
6 5 4 3 2 1 // as we can go from left to right from somewhere
```

Now looking at the snowflakes and trying to see if they are the same, is going to take a lot of time, there really is no way of getting around going through the arrays, starting at various points. That is just going to take a lot of time. 
If we have 100000000 snowflakes, each with 6, and we have to go forward and backwards, and we have to start at various points. That is literally gonna take minutes. 
Well, if have to look at two snowflakes cannot really be  made easier, then we will have to look at reducing the number of snowflakes that we are looking at. 
That's the thing, if we know that a comparison is going to be tough, it really just takes a lot of time in order to look at it, there is no way around that, then we have to look at reducing the number of snowflakes that we compare. 

> **Whenever we find ourselves working on something where we are repeatedly searching for some element (of the problem, not just in an array), consider using a hash table. The random access of it, means that we can find what we have produced before.**

###### What do we HASH: 
In this example, two snowflakes will technically be very similar if their elements sum to the same number. They will not be the same, as that is something that we will leave to compare, but we can bundle that ones that we have narrowed down that can be similar. 
Then using a hash table that takes in the sum of the snowflakes in order to reduce the number of comparisons. 
Remember that a hash table is just an array, where each index is  `bucket`, and then we can chain address, this is closed addressing. 
Open addressing is where we don't used linked lists, and rather a probing technique until we find the next open index of the array. This can be harder to implement, however, if you don't want to write your own linked list, then that's fair enough. 

The idea that we can absolutely smack into a hash table as much as we want. 

###### Dynamic Arrays
In C++, map is a dynamic array. Meaning that it will grow as we need it to. 
```
template < 
	class Key,
	class T, 
	class Compare = std::les<Key>, 
	class Allocator = std::allocator<std::pair<const Key, T>>
>
```
In this map, search, removal and insertion operations have logarithmic complexity. 

### Spelling Check
Here is a problem that looks like it would really benefit from a hash table, however, it really doesn't. Giving us a better idea of when to use them, and when to not. We have already said that we can use them if we are going to repeatedly look up some element. 

The idea with this one is that we have two string, the first will be one char longer than the second. We have to go through and say how many ways the first can be changed in order to make the second. 
```
abcdxxxef
abcdxxef
There are three ways to make the first into the second, one of the three x's can be removed 
```
Then the program also wants the index of the letters that we can change in order to get there. 
The guy was trying to use a hashing table in order to make this work much better. 
However, if the strings are almost millions of chars, then the hash table will be huge. 
He was trying to make a hash based on the prefix, and by adding the char's value to the previous hash. 
Calculating the hash for any substring is efficient, as we are using an incremental hash function, meaning that the last hash is taken into consideration for the new one, if they are the near the same (bar the last char in this case). 
That's what this code here represents: 
```
unsigned long long prefix[1000001];
prefix[0] = 0
for(i = 1; i <= strlen(first_string); ++i)
	prefix[i] = prefix[i - 1] * 39 + first_string[i];
```
Here we are using the last hashed element in order to work out the new one: 
Peak this: 
```
first_string = "abc";
as prefix[0] = 0; i'm guessing if the string is empty
then go through :
// a is equiv to 97
prefix[1] = prefix[0] x 39 + 97 = 0 x 39 + 97 = 97

// b is obviously 98
prefix[2] = prefix[1] x 39 + 98 = 97 x 39 + 98 = 3881

// c is obviously 99
prefix[3] = prefix[2] x 39 + 99 = 3881 x 39 + 99 = 151458
```

Therefore for two substrings, if they are the same, they will have the same hash, they will end up at the same place. 
The choice of `39` is arbitrary, but common in hashing, doesn't really say why. 

This is really nice now, as we have two prefix arrays (almost serving as hashing tables) that both represent that idea that as we go through a string, we are adding the value of the next char in. 
This means, that for any given `i` we can see if the two prefixes of that string are equal: 
```
prefix1[i] == prefix2[i]; 
if true then we know that the prefixes are going to be equal. 
```
This means that `prefix[i]` will give the hash of the substring from the start of `first_string` to `i`. 

Then if we want to find the hash of the substring between `first_string[j], first_string[i]`, then we can do this: 
`substring_hash = prefix[i] - (prefix[j - 1] x 39^{i - j + 1})`
Apparently, the 39 is due to the fact that it didn't cause any collisions in that one codeforces problem, lol. 
However, there is a better way: 

The general idea that first comes to mind, is that the first divulsion from each other is going to be where we need to change a char in order to get to the second string, then we just see how many letters are similar and then get all those indexes. 
If there is a divulsion at p, then no changing at > p will be useful. 
Here we have to take a look at the longest common substring. 
Then we can notice something interesting is that if we take n as a length of the string, then take away the length of the longest substring, then we have the upper bound of the different char. 
If there is only char that is different, and the first string is longer. 

This will stop on the letter that is different: 
```
int compareTwo(string& A, string& B)
{ 
	int i = 0, length = 0; 
	while(A[i] == B[i])
	{ 
		++length;
		++i;
	}
	return length;
}
```
This will give the index of the first letter that is different. 

Given this:
```
abcdxxxef
abcdxxef
The answer will be 6
```

Then we could also have a look at the longest common suffix, then that middle part, the number of recurring chars, is what we have to return. 


Getting the longest suffix length: 
```
int suffix_length(string& A, string& B)
{ 
	int len = A.lehth(); 
	while(i > 1 && A[i] == B[i - 1])
		i--;
	return len - i; 
}
```

The guy used a whole hash map in order to figure out the prefix and the suffix lol. 
This method is just easier: 
```
int main() 
{ 
	string a, b;
	std::getline(std::cin, a);
	std::getline(std::cin, b);

	// or as they are just whole words, then we can just use cin >> a 
	// cin >> b
	int prefix, suffix;
	prefix = prefix_length(a, b);
	suffix = suffix_length(a, b);
	int total = (prefix + 1) - (len + suffix) + 1;
	if(total < 0)
		total = 0; 
	for(int i = ; i < total; ++i)
	{ 
		cout << (i + len - suffix) << " ";
	}
}
```
Here there is the possibility of using a search with a hash table, however, it's not really necessary. 
There is something about the problem here the obviates the use of one.

