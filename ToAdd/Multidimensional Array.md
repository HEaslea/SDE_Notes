Strictly there are none of these in C++. 

Rather, there are arrays of arrays. 

`int ia[3][4]; // array of size 3; each element of this array is an array of ints of size 4`
`int arr[10][20][30] = {0};`
An array of size 10 ,each element is an array of 20, whose elements are arrays of 30 ints, and all elements are init'd to 0. 
Again, we read stuff inside out and right to left, which is a little odd, but it's basically python MD lists. 

`arr` is an array of size 10. The elements of this array are themselves arrays of size 20. Each of those arrays has 30 elements that are of type `int`. 

There isn't a limit to the number of subscripts. 

In 2D, the first is considered the row and the second is the column. 

![[Pasted image 20240216201026.png]]

The nested braces are optional. 

It is equivalent to this, however, this is much less clear: 
`int ia [3][4] = {0,1,2,3,4,5,6,7,8,9,10,11};`
Notice that there are 12 elements. 

![[Pasted image 20240216201139.png]]

![[Pasted image 20240216201456.png]]


This is the basic read out, nested for loops. 
If it were `int ia[3][4] = {0,3,6,9};`, this is just initializing the first row. 
`int ia[r][c] = {{0,3,6,9},{2,4,6,8},{1,2,3,4}};` this works for full initialization as well. 

Accessing the elements, just using subscripts. If there are as many subscripts as there are dimensions, then we get the element that it corresponds to. 

`cout << ia[3];` would output the memory address for, I'm assuming, the 3rd row. 
 The idea is that `ia[2]` will return an array, then we can further subscript that array as well.

`int (&row)[4] = ia[1];` row is a reference to an array of four ints. We bind that reference to the second row in `ia`. 

![[Pasted image 20240216204441.png]]
Of course, the outer for is numbering rows, meaning that `i * colCnt` for the first row is 0, then + the j for the column, meaning that the array will have 3 arrays, of 4 elements, that will be numbered from 0 to 11. 

### Using a `range for` loop
```
size_t cnt = 0;
for (auto &row : ia){ 
	for (auto &col : row){ 
		col = cnt; 
		++cnt
	}
}
```

Remember that `size_t` is just a special unsigned int type. 
It is a type that is returned from quite a few functions. 
Same as the loop well above. 

```
for (const auto &row : ia){ 
	for (auto col : row){ 
		cout << col << endl; 
	}
}
// however, we can't do this
for (auto row : ia)
	for (auto col : ia)
// our program wouldn't compile
```
In the second one, because row is not a reference, when the compiler initializes row it will convert each array element to a pointer to the array's first element, not the actual row in the array. 
REMEMBER that when we use an array, we have to think about when it will be a pointer to the first element. 
Overall, the innermost dimension will have to be a reference. 

![[Pasted image 20240216210309.png]]
![[Pasted image 20240216210322.png]]

![[Pasted image 20240216210341.png]]
There was a bit of a problem when writing in that the j for loop needs to be a reference as well. Essentially, only the innermost for loop need not be a reference. What you wrote would've tried to iterate over a pointer to the 2D row. 

![[Pasted image 20240216210727.png]]

### Pointers to
```
int ia[3][4]; 
int (p*)[4] = ia; // p points to an array of four ints
p = &ia[2]; // p now points to the last element in ia.
```
`(*p)` says p is a pointer. The object to which it points is a size of 4, and the type is int. Hence p is a pointer to an array of four `ints`.

```
int *ip[4]; // array of pointers to int
int (*ip)[4]; // pointer to an array of four ints
```

Using auto and decltype is great for this very thing. 

```
for (auto p = ia; p != ia + 3; ++p){ 
	for (auto q = *p; q != *p + 4; ++q){ 
	cout << *q << " ";
	}
	cout << endl;
}
```
p to point to the first array in ia. That loop continues until we've processed all three rows in ia. 
Remember that ++p will move onto the next row. 
Then q will point to the first element in the row (by using the dereferenced p). This is because `*p` is dereferenced to a whole array. Then this is shortened to being the pointer to the first element of that row. 

Then we can do this with the begin and end functions: 
```
for(auto p = begin(ia); p!=end(ia);++p){
	for (auto q = begin(*p); q != end(*p); ++q){ 
	cout << *q << "  ";
	}
	cout << endl;
}
```

Here we let the library determine the end pointer, and auto to avoid having to write the type return from begin. 
The outer loop, the type is a pointer to an array for four `ints`. 
The inner is a pointer to an `int`. 

#### Using Type Aliases
```
using int_array = int[4]; 
tyepdef int int_array[4]; //this is the same as the line above weirdly

for (int_array *p = ia; p != ia + 3; ++p){ 
	for (int *q = *p; q != *p + 4; ++q){ 
		cout << *q << endl; 
	}
	cout << endl;
}
int_array i = {0,1,2,3}; // due to the type alias, this is fine
```
`int_array` is another name for array of four ints.  The first one is a lot more readable.
The chapter summary is actually really useful here. 

