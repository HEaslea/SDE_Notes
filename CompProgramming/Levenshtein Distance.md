Given the nature of the problem, it would be better to use **fzf-style** for the program that we are writing.
Levenshtein might be too much, overkill. 

`rdme -> README.md`

[[FZF Style Fuzzy Finding]]

This is in reference to finding how close two strings are together. 

Measuring the minimum number of single-character edits, needed to turn one string into another. 

This includes any of: 
- Insertion 
- Deletion
- Substitution

Given: 
```
"kitten" -> "sitting"

sub 'k' -> 's' (1)
sub 'e' -> 'i' (2)
insert 'g'     (3)

Distance = 3
```

This `Distance` is going to be the most important when it comes to ranking. 

Really good for **fuzzy** searching when we want near-matches. 

### How to go about it
The main idea is building a DP Table (dynamic programming table). 
[Wiki on it](https://en.wikipedia.org/wiki/Levenshtein_distance)

[LeetCode Problem](https://leetcode.com/problems/edit-distance/) I'm pretty sure this is the closest one. 

Essentially measuring the difference between the two strings. 

![[Pasted image 20250808000517.png]]
Given that the tail of some string `x` is all but the head, which is the first char, `head(x) = x0 = x[0]`, all just different notations. 

Then the wiki just goes through the `kitten` and `sitting`example.

There are considerations to the upper and the lower bounds: 
- Will be, at most the length of the longer string. 
- 0, if they are equal, in every way that you could query. 
- Hamming distance, if the strings have the same size. This is the number of positions at which the two strings are different. 
- The distance between two strings will not be greater than the sum of their distances from a third string, as you move to get to the middle string, and then move to the other, A -> C <- B. 

#### Matrix Approach
The main idea here is that we create a matrix based on the length of the two words, so height is length of a, and width is length of b. 







