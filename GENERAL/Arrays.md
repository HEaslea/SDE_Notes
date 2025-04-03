#Arrays
Very similar to the `vector` type library, offering a different trade of between performance and flexibility. 

THEY WILL HAVE FIXED SIZES; 

Better run-time performance, yet worse flexibility. 

If you don't really know how many elements that you would need, then you would use a vector. 

 They are a [[Compound Type]]. 

```
unsigned cnt = 42; // not a constant expression
constexpr unsigned sz = 42; // constant expression
int arr[10]; // array of ten ints
int *parr[sz]; // array of 42 pointers to int
string bad[cnt]; // ERROR: cnt is a not a constant expression
string strs[get_size()]; // fine if get_size() is a constant expr
```

[Constant Expression]

Like with vectors, there can be no array of references, they need to be objects. 

Explicitly initializing them: omitting the dimension, inferred by the elements in the list. 
If the dimension is longer than the list initializer elements provided, then the remaining elements are value initialized.

```
const unsigned sz = 3; 
int ia1[sz] = {0,1,2}; // an array of three ints with values 0,1,2
int a2[] = {0,1,2}; // an array of dimension 3
int a3[5] = {0,1,2}; // equivalent to a3[] = {0,1,2,0,0};
string a4[3] = {"hi", "bye"}; // same as a4[] = {"hi", "bye", ""};
int a5[2] = {0,1,2}; // clearly wrong 
```

Character arrays can be initialized from a string literal too. 
Remember that the string literal will end with a null character. This will be carried into the array too. 

```
char a1[] = {'C', '+', '+'}; // list initialization with no null
char a2[] = {'C', '+', '+', '\0'}; list initialization, explicit null 
char a3[] = "C++"; // null terminator added automatically
const char a4[6] = "Daniel"; // error ! no space for the null!
```

You cannot initialize an array as a copy of another, no can you assign one array to another. 

```
int a[] = {0,1,2}; // array of three ints
int a2[] = a; // error cannot initialize an array with another
a2 = a; // another error, can't assign one array to another. 
```

Understanding complicated array declarations: 
You can have an array of pointers. As an array is an object, we can have references and pointers to them as well. 

```
int *ptrs[10]; // an array of ten pointers to int
int &refs[10] = // just can't do this obviously, to refs to what? 
int (*Parray)[10] = &arr; // Parry points to an array of 10 ints
int (&arrRef)[10] = arr; // arrRef refers to an array of ten ints. 
```

Easier to read the last ones as inside out, rather than left to right first, like a code form of BIDMAS. 
Brackets, then right to left. 

So `Parray` is a pointer, to an array of size 10, then we see that the elements are int. 

`int *(&arry)[10] = ptrs; // arry is a reference to an array of ten pointers. 

Also looking at the name first helps it too. 

We can take the address of the elements in the array. 

```
string nums[] = {"one", "two", "three"}; // array of strings
string *p = &nums[0]; // p points to the first element in nums
```
Arrays will have a special property - in most places when we use an array, the compiler automatically subs a pointer to the first element: 
```
string *p2 = nums; // equivalent to p2 = &nums[0];
```
imho, less readable but whatever. 

```
int ia[] = {0,1,2,3,4,5,6,7,8,9};
auto ia2(ia); // ia2 is an int* that points to the first element in ia
// we can use an array like that, as arrays can't be copied to another
// or an array to be used to initialize another, it just carries as a pointer
// to the first element
ia2 = 42; // can't assign an int to a pointer. 
// it's treated like this
auto ia2(&ia[0]) // no it's clearer that ia2 has type int*
```

HOWEVER THIS DOESN'T HAPPEN WHEN WE USE DECLTYPE, THAT WILL RETURN AN ARRAY OF 10 INTS.

```
// ia3 is an array of 10 ints
decltype(ia) ia3 = {0,1,2,3,4,5,6,7,8,9};
```


Pointers addressing elements have an addition operation. 
Most notably, the idea that they work like iterators on vectors or strings. 

Meaning that we can use the increment operator in order to move through an element to the next. 

```
int arr[] = {0,1,2,3,4,5,6,7,8,9};
int *p = arr; // p points to the first element in arr
++p; // p points to arr[1]
```
An "off the end pointer" would look something like this; 
`int *e = &arr[10]`
Here we just used an index to a non existing element; `arr` has ten elements, so the 10<sup>th</sup> subscript will be a non-existing element. 

Don't dereference it. 
Using pointers to iterate through. 

```
for (int *b = arr; b! = e; ++b){ 
	cout << *b << endl; 
}
```

However we do also have a begin and end function to make these pointers easier for us. 

```
int ia[] = {0,1,2,3,4,5,6,7,8,9}; 
int *beg = begin(ia); // pointer to the first element in ia
int *last = end(ia); // pointer one past the last element in ia
```

These are still defined in the iterator header. 

```
// pbeg points to the first and pend just past the last element
int *pbeg = begin(arr), *pend = end(arr);
// finding the first negative element
while(pbeg != pend && *pbeg >= 0){ 
 ++pbeg;}
```
Like with the vector iterators, we can operate on our pointers with arithmetic. 
This makes all the stuff that you read about in A level much easier to understand. 

```
constexpr size_t sz = 5; 
int arr[sz] = {1,2,3,4,5}; 
int *ip = arr; // equiv to int *ip = &arr[0]; 
int *ip2 = ip + 4; // ip2 points to arr[4], the last element in arr
```

`arr` initializes `ip` to the pointer of the first element. 

```
auto n = end(arr) - begin(arr); //n is 5, the number of elements in the arr
```

The type for this is a little bit different, it's called `ptrdiff_t`, like `size_t`, the `ptrdiff_t` is a machine-specific type, and is defined in the `cstddef` header. 
As subtraction might return a negative number, it has to be signed, integral type. 

Then we can compare the pointers, as you would an iterator. 

```
int *b = arr, *e = arr + sz;
while (b < e) { 
// use *b
	++b
}
```

This is something that you cannot do with two unrelated objects and their pointers ie: 
```
int i = 0, sz = 42; 
int *p = &i, *e = &sz; 
while (p < e) // undefined: p and e are unrelated; comparison here is meaningless. 
```
Pointer arithmetic is also valid for null pointers as well. 

```
int ia[] = {0,2,4,6,8}; // array with 5 int elements
int last = *(ia + 4); // ok initializes last to 8, the value of ia[4]
last = *ia + 4; // ok : last = 4, equivalent to ia[0] + 4
```
When we subscript an array, we are really subscripting a pointer to an element in that array. 
```
int i = ia[2]; ia is converted to a pointer to the first element in ia
// ia [2] fetches the element to which (ia + 2) points
int *p = ia; // p points to the first element in ia
i = *(p + 2); // equivalent to i = ia[2]
```

Then we can do some funky stuff like this: 
```
int *p = &ia[2]; // p points to the seconed element in ia
int j = p[1]; // p[1] = *(p+1)

```