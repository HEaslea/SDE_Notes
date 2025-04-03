When we use an algorithm that assigns to elements, we must take care to ensure that the sequence into which the algorithm writes is at least as large as the number of elements we ask the algorithm to write. 
Remember that algs do not perform container operations, so they have no way themselves to change the size of the container. 

Algs that write in the input range are not inherently dangerous, they write only as many elements as are in the specified range. 
The `fill` alg takes a pair of iterators that denote a range and a third argument that is a value. 
That alg assigns the given value to each element in the input sequence. 

Algs that take a single iterator to denote the second sequence **assume** that the second sequence is at least as large as the first. It is up to us to ensure that the alg will not attempt to access a nonexistent element :'(. If the second container is a subset of the first, then we will run into a serious error. `equal` will then attempt to access elements beyond the end of the second sequence. 

```
fill(vec.begin(), vec.end(), 0); // reset each element to 0
// set a subsequence of the container to 10
// we can do half the size of the container
fill(vec.begin(), vec.begin() + vec.size()/2, 10);
```


```
std::vector<int> nv(20,0);
fill(nv.begin(), nv.begin() + nv.size()/2, 100);
/*
10 : 10 : 10 : 10 : 10 : 10 : 10 : 10 : 10 : 10 : 0 : 0 : 0 : 0 : 0 : 0 : 0 : 0 : 0 : 0 :
*/
```

#### Algorithms do not check Write Operations
Some algs take an iterator that denotes a separate destination. 
These assign new values to the elements of a sequence starting at the element denoted by the destination iterator. 
The `fill_n` function takes a single iterator, a count, and a value. 
```
vector<int> vec; //empty
// use vec giving it various values
fill_n(vec.begin(), vec.size(), 0); // reset all the elements of vec to 0
```
These algs assume it is safe to write the specified number of elements. 
`fill_n(dest, n, val)`
Assumes that the dest refers to an element and that there are at least n elements in the sequence starting from `dest`.
THEREFORE it is a mistake to call it on an empty container. 
```
vector<int> vec; // empty vector
// disaster: attempts to write to ten (nonexisten) elements in vec
fill_n(vec.begin(), 10, 0);
```
This, above, is a sin. We specified that ten elements should be written, but there are no such elements - `vec` is empty. The result will be undefined. 

Algs that write to a destination iterator *assume* the destination is large enough to hold the number of elements being written. 


```
int arr[5];

std::fill_n(arr, 3, 10); // 10 10 10 23424635 345346345 the last two are undefined
```
The idea mostly is that we have to have a size, if we try to fill without one, something funky might happen, or it's undefined. 

```
// you couldn't do something like this 
std::vector<int> v(10);
std::fill_n(v.begin(), 20, 3);
// undefined behaviour behind the vector etc. 
// do something like this as well 
std::fill_n(v.begin(), v.size(), 0);
```


```
std::vector<int> v(100);
std::fill_n(v.begin(), v.size(), 20);

for(int i : v){ 
	std::cout << i << "   ";
}
std::cout << std::endl;
```

We can also do something like this: 
```
std::vector<int> vec = {1,2,3,4,5,6,7,8,9};
std::fill_n(vec.begin() + 2, 4, 100);
```

Here is a thing: 
```
std::vector<int> v(100);
std::fill_n(v.begin() + 10, v.size()-10, 5);
```

This means that we go from the 10th element to the end. 
if we did something like this: 
```
const int size = 20; 
const int half = size/2; const int edge = size/4;
std::vector<int> v(size);
std::fill_n(v.begin() + edge, v.size() - half, 100);
// print out
0  0  0  0  0  100  100  100  100  100  100  100  100  100  100  0  0  
0  0  0
```
Pretty cool for a number of reasons, that we can add elements in the way that will buffer the edges based on the calcs that we do in the call. 

### Introducing `back_inserter`
#back_inserter
An insert iterator is an iterator that *adds* elements to a container. 
When we assign through an insert iterator, a new element equal to the right-hand value is added to the container. More on these later. 
`back_inserter` is used in `iterator` header. 

`back_inserter` takes a reference to a container and returns an insert iterator bound to that container. 
When we assign through the iterator, the assignment calls `push_back` to add an element with the given value to the container: 
```
vector<int> vec; // empty vector
auto it = back_inserter(vec); // assigning through it adds elements to vec
*it = 42; // vec now has one element with value 42
```

Essentially the idea is that we use the iterator as a push_back insertion method. 

This here is just copying from one vec to another: 
```
vector<int> vec1 = {1,2,3}, vec2;

auto backInserter = back_inserter(vec2);

for(auto it = vec1.begin(); it != vec1.end(): ++it){ 
	*backInserter = *it; // inserting into vec2, backInserter for vec2
}
```
They will both be 1 : 2 : 3 :. 

```
bool isEven(int x){ return x % 2 == 0}
int main(){ 
	vector<int> source, destination; 
	for(int i = 0; i < 100; ++i){ 
		source.push_back(i);
	}
	auto backInserter = back_inserter(destination);
	for(auto i = source.begin(); i != source.end(); ++i){ 
		if (isEven(*i))
			*backInserter = *i;
	}
	// print vect
}
```
0 : 2 : 4 : 6 : 8 : 10 : 12 : 14 : 16 : 18 : 20 : 22 : 24 : 26 : 28 : 30 : 32 : 34 : 36 : 38 : 40 : 42 : 44 : 46 : 48 : 50 : 52 : 54 : 56 : 58 : 60 : 62 : 64 : 66 : 68 : 70 : 72 : 74 : 76 : 78 : 80 : 82 : 84 : 86 
: 88 : 90 : 92 : 94 : 96 : 98 : 
That is our output: 

Using this idea to create an iterator to use as the destination of an algorithm;
```
vector<int> vec; // empty 
// this is ok as this will create an insertion iterator that adds elements to vec
fill_n(back_inserter(vec), 10, 0); // this will append now
```
So `fill_n` will only overwrite elements, unless used with a back_inserter.
```
vector<int> vec = {1,2,3,4,5};

fill_n(vec.begin(), 10, 100);
```
This will just produce : 100 100 100 100 100

However if we were to append them using a back_inserter iterator here: 
```
vector<int> vec = {1,2,3,4,5};
fill_n(back_inserter(vec), 10, 100);
```
this will look more like: 1 2 3 4 5 100 100 100 100 100 100 100 100 100 100 
10 100's at the end. 

This means that we will call `push_back` on vec. 

### Copy Algorithms
Taking three arguments: as three iterators. 
The first two denoting the input range; the third denotes the beginning of the destination sequence. 
This algorithm copies elements from its input range into elements in the destination (remember that we can use the back_inserter iterator at the end of the args in order to push back in the new container). Of course not with arrays lol, as they do not have a push_back.
It is, again, essential that the destination passed in is at least as large as the input range. 
```
int ia[] = {0,1,2,3,4,5,6,7,8,9};
ia a2[sizeof(a1)/sizeof(ai[0])]; // a2 has the same size of a1
// ret points just past the last element copied into a2
auto ret = copy(begin(a1), end(a1), a2); // copy a1 into a2
```
Here ret will point to just past the last element copied into a2. 

`replace` is another one, within the iterator range, we can change all the elements of a certain value to another value that we define in the args. 

```
// replace any element with the 0 with 42
replace(ilst.begin(), ilst.end(), 0, 42);
```
```
// use back_inserter to grow destination as needed
replace_copy(ilst.cbegin(), ilst.cend(), back_inserter(ivec), 0, 42);
```
`replace_copy` This takes a third iterator for another container, in this case ivec, that we append the replaced values from `ilst`. `ilst` is unchanged, and `ivec` contains a copy of `ilst` with the exception that every element in `ilst` with the value 0 has the value 42 in `ivec`.

