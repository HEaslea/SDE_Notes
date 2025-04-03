Remember that in C#, there is the idea of rectangular and jagged arrays. 
#### Rectangular Arrays
These are arrays that do have a fixed size in both dimensions. This means that all rows have the same number of elements. 
`int [,] rectangularArray = new int[3, 4]; // 3 rows 4 columns`
Accessing is simple: `rectangularArray[0, 0] = 5`. 

Then there are: 
#### Jagged Array 
`int [][] jaggedArray = new int[3][]; // declare with 3 rows` 
```
jaggedArray[0] = new int[4];
jaggedArray[1] = new int[2];
jaggedArray[2] = new int[3];
```
Then we access slightly differently: 
`jaggedArray[0][0] = 5;`. 

Anyways, let's move on to the book and it's renditions of C++. 

in C++, we obviously refer to `A[i][j]`, meaning row `i` and column `j`. 

### The Sudoku Checker Problem
Essentially, given a sudoku, just check that it is correct. 
Given that there are 1-9, 0 says that the square is blank, not representing a number. 

Apparently this is no real scope of algorithm optimization here, considering that the code is quite messy, given that we have tired to solve this before, and it was very difficult indeed. 
The goal is just writing clean code, isn't it always, however, here it is because 
Obviously given the number that we are looking at, at any point, we have to check the row, the column, and the 3x3 box that it resides in. 
Apparently the best way to check for the row is with bit arrays. 
I think the main thing s that we are checking the row, the columns and the regions, somewhat separately. 
Then there is the helper function, `HasDuplicate`.
```
// check if the partially filled matrix that we have been given has any conflicts
bool isValidSudoku(const vector<vector<int>>& partial)
{ 
	// checking row constraints
	for(int i = 0; i < size(partial); ++i)
	{ 
		if(HasDuplicate(partial, i, i + 1, 0, size(partial)))
		{ 
			return false;
		}
	}

	// checking column constraints
	for(int j = 0; j < size(partial); ++j)
	{ 
		if(HasDuplicate(partial, 0, size(partial), j, j + 1))
		{ 
			return false;
		}
	}

	// checking region constraint
	int region_size = sqrt(size(partial)); // given that square sudoku
	for(int I = 0; I < region_size; ++I)
	{ 
		for(int J = 0; J < region_size; ++j)
		{ 
			if(HasDuplicate(partial, region_size * I, 
				region_size * (I + 1), region_size * J,
				region_size * (J + 1)))
				return false;
		}
	}
	return true;
}
```
I think the key here is that `HasDuplicate` is a helper function, in that we are searching through a group of numbers, which can be defined before hand, and we have to see if any of those numbers appear twice. 
Now this is how we did it as well: 
I think there is some merit to separating out the two, and not trying to get too much into a single sweep of the sudoku. 
Just the fact that solution in the book was so easy to read, means that the way that we solve this here is really simple. 
Then we have some form of optimization in the form of the helper function, which looks something like this: 
```
// Return true if subarray partial [start_row, end_row - 1]
// start[start_col, end_col - 1]  contains duplicates { 1, 2, ... , size(partial)};
bool HasDuplicate(const vector<vector<int>>& partial, 
	int start_row, int end_row, int start_col, int end_col)
{ 
	deque<bool> is_present(size(partial) + 1, false); // using a bit array
	for(int i = start_row; i < end_row; ++i)
	{ 
		for(int j = start_col; j < end_col; ++j)
		{ 
			if(partial[i][j] != 0 && 
			is_present[partial[i][j]])
				return true;
		}
		is_present[partial[i][j]] = true;
	}
}
```

The helper function can help more than just this problem. This means that we do have to arg more than just the start and end, and can't hard code the idea of the size of the sudoku being 3 x 3. 
Writing the helper function, makes the rest of it make so much more sense. 
The space complexity is filled with the bit array that we have made. 
The time complexity is going to be mostly filled by the nested for loop. 

#### Compute a Spiral Order of a 2D Array
Imagine a square array, say a 4x4, 
```
1  2  3  4 
5  6  7  8
9  10 11 12 
13 14 15 16
```
The spiral order of this would be `1 2 3 4 8 12 16 15 14 13 9 5 6 7 11 10`
Then for a 3x3 given the same ordering of the numbers, that would looking something like this: 
`1 2 3 6 9 8 7 4 5`. 
the hint tells us that we should be dividing and conquering, using case analysis. 
The first row is obvious. 
One thing I thought about here is that we are just printing at a row of numbers. 
The other thing I thought about was using states to denote movement, then using a `++` goes right if we are to go right, down if we are to go down, and left to go left etc then we can adapt this to all the ways, then we just need to iterate for the size of the array. 

Let's say that we have an array of those enums `{RIGHT, DOWN, LEFT, UP}`
Perhaps then you can do an index of that, grad whichever it is, then we can do a wrap around. 
You know how obvious that is `i % 4` -> 3 2 1 0 etc. 
See the pink postit notes. 
We have to think about how square matrices are different than rectangular. The idea with the squares is that we work with the outer, then we have another square in the inner part that we have to look at, what do we have here, some fucking badass recursion here: we take the spiral of the inner most square, then append to the front the spiral order of the square larger than that, then the square larger than that etc. etc. 

```
vector<int> MatrixInSpiralOrder(const vector<vector<int>>& square)
{ 
	vector<int> spiral_ordering;
	for(int offset = 0; offset < ceil(0.5 * size(square)); offset++)
	{ 
		MatrixLayerInClockwise(square, offset, &spiral_ordering)
	}
	return spiral_ordering;
}
```

Then there is this whole fucking loop right here: 
```
void MatrixLayerInClockwise(const vector<vector<int>>& square, 
	int offset, vector<int>* spiral_ordering)
{ 
	if(offset == size(square) - offset - 1)
	{ 
		// square matrix has odd dimensions and we are at the center of the square matrix
		spiral_ordering->emplace_back(square[offset][offset]);
		return;
	}
	for(int j = offset; j < size(square) - offset - 1; ++j)
	{ 
		spiral_order->emplace_back(square[offset][j]); 
		// going through the row it seems
	}
	for(int i = offset; i < size(square) - offset - 1; ++i)
	{ 
		spiral_ordering->emplace_back(square[i][size(square) - offset - 1]);
	}
	for(int j = size(square) - offset - 1; j > offset; --j)
	{ 
		spiral_ordering->emplace_back(suqare[size(square) - offset - 1][j]);
	}
	for(int i = size(square) - offset - i; i > offset; --i)
	{ 
		spiral_ordering->emplace(square[i][offset]);
	}
}
```
The first time around, the offset will be 0, meaning that the first time, will be n - 1. 
The size of the offset is never going to be bigger than ceil(0.5 * n) of the n x n square. 

The guy on youtube was talking about thinking of lines that we follow as we go through the matrix. 
Thinking this through: 
