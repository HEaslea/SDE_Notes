All from the fantastic O'Reilly book, "Machine Learning with Python Cookbook". 


#### Vector

```
import numpy as np

vector_row = np.array([1, 2, 3]) # taking in a list

vector_column = n.array([1], [2], [3]) # column
```
Vectors are just arrays, with a single dimension. 

#### Matrix
Creating a two dimensional array: 
```
import numpy as np

matrix = np.array([[1, 2], [1, 2], [1, 2]])
```

#### Creating a Sparse Matrix
Basically the same thing: 
```
import numpy as np
from scipy import sprase

matrix =np.array([[0, 0], [0, 1], [3, 0]])

matrix_sprase = sparse.csr_matrix(matrix)
```

Huge amount of data, however, a lot of it will be 0's. 
If you imagine a vector of all the movies on netflix, and a number to show how many times that person has watched it, there would be a lot of 0's. 

Sparse - when most elements are 0. 

Sparse matrices only store nonzero elements assume all the other values will be zero. 
Saves a lot of space, and a lot of computations. 

So we make a matrix, then convert it into a sparse matrix. 

We would see something like this: 
```
print(matrix_sparse)

(1, 1)   1
(2, 0)   3
```

There are a few types, **compressed sparse row** (CSR), the `(1, 1)` and `(2, 0)` represent the zero-indexed indices of nonzero values 1 and 3 respectively, everything else is presumed to be 0. 

```
matrix_large = np.array ([[0,0,0,0,0,0,0,0,0,0], 
						[0,1,0,0,0,0,0,0,0,0], 
						[3,0,0,0,0,0,0,0,0,0]])

matrix_large_sparse = sparse.csr_matrix(matrix_large)

print(matrix_large_sparse)

(1, 1)   1
(2, 0)   3
```
This is the same as the other, despite there being many more zeroes here than before.

There is no best sparse matrix, however, there are different types that work best, and they have meaningful differences. 


#### Preallocating NumPy Arrays
We can just create arrays with 0's and 1's. 

```
import numpy as np

vector = np.zeroes(shape = 5) # (1, 5) containig all zeroes (r, c)

print(vector)

# array([0, 0, 0, 0, 0])

matrix = np.full(shape=(3, 3), fill_value=1)
print(matrix)

# array ([[1, 1, 1], 
		[1, 1, 1], 
		[1, 1, 1]])
```
This is pretty common practice. 

#### Selecting elements
```
import numpy as np

vector = np.array([1, 2, 3, 4, 5, 6])
matrix = np.array([[1, 2, 3], 
					[4, 5, 6], 
					[7, 8, 9]])

print(vector[2]) # pretty simple using indexing with []
# 3

print(matrix[1,1]) # not the same as C++ I don't think
# 5
```