#### Placeholder
`...` Placeholder, indicating unfinished code or a part of a function/method that needs to be implemented later. 
```
def some_function(): 
	...
```

#### Slicing in Multi-dimensional Arrays eg. NumPy
When working with high dimensional  arrays, reducing the number of explicit slices you need to write: 
```
import numpy as np
arr = np.random.random((5, 5, 5))
print(arr[..., 1]) # selecting along the first two axes and the second index along
the first two axes and the second index along the third axis
```
