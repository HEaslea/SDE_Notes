[Leetcode Problem](https://leetcode.com/problems/spiral-matrix)

This one was fairly simple to deal with and understand, however, not abundantly clear at the beginning.
The trickiest part was finding when to exit, at least in my head. 

The idea is that we have four boundaries: with matrices, if we have a value representing a column or a row, then we are representing a bunch of numbers in one go, unlike with vectors, where we just have one "row" and then a bunch of indexes that just relate to the one number, due to the one dimensionality of the vector. 

Say that we have: 
```
{{1, 2, 3}, {4, 5, 6}, {7, 8, 9}}
```

Which relates to: 
```
1 2 3
4 5 6
7 8 9
```

This is 0 indexed, which always adds a small layer of obfuscation. 

Say we are relating to the column `2` : then that relates to the numbers `3, 6, 9`, if we think about row `1`, that relates to `4, 5, 6`. 

The spiral matrix, then is made rather easy, if we imagine that we go right, down, left, up, and we just put boundaries down to give us the range that we are using. 

The first thing we do is set the first boundaries, the first movement being from left to right, 

```
int left = 0; // referring to the column
int rigt = matrix[0].size(); // square matrix, so 0 could be any of them,
// NOTE: right is one past th eright column, therefore we use < in for loop
```
Then let's have a look at the row that we are representing, `bottom` and `top`. 

```
// these refer to the rows
int top = 0; // duh
int bottom = matrix.size() // get the outer
```

The first time that we go through, we have done the top row, we don't need that again. Therefore, we can say `top++`, so that that boundary moves down one. 

The order is `left->right top++, top -> bottom right++, right -> left bottom++, bottom -> top left++`

One thing to notice is when we stop the loop. 
The easiest way to think about it, and is something that we can think about more often is writing for loops that don't actually run, due to the indexes used as the two pointers: 

You will see here, that some of the for loops that we use will not be called, because `left == right - 1`,  and similarly for `top, bottom`, therefore, we will consider a full rotation, back to near the origin as the complete look and we know that if either boundarhy

