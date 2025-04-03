Going over Arrays, Vectors etc. etc. 

[This Video](https://www.youtube.com/watch?v=6OoSgY6NVVk)
## Array
```
int a[10]; Memmory allocated on the stack
int *b = new int[10]; Memory allocated on the heap

// memory on the heap will need to be freed up
delete[] b; 
```
If the stack variable goes out of scope, then we don't need to worry about that memory, as it will be freed up for us. 

Stack; stores local variables
Heap; dynamic memory for programmer to allocate. 

The idea that when we create this array, there is somewhere, a pointer to the start of the array, &a. An int takes 4 bytes, therefore, we would need 40 bytes in order to store just those arrays. 

Arrays need to be contiguous. Meaning that we can do the whole arithmetic idea. 

With arrays written like this, it's entirely legal to write `a[17]`. 

`#include <array>`

`std::aray<int, 10> b;`

When init from the library, the idea of `b[18]` would return a compiler error. 

The arrays that we have used so far, have all been "built-in" types. However, using the library version of the array, would mean that we instantiating classes. 

Therefore something along the lines of: 
```
std::aray<int,10> b; 
b.size(); 
```
Is a thing that you can do. Just note that the methods will work slightly differently here, than the methods that we used on other arrays. 

```
cout << b.size() << endl;
```
Would return number of elements. 
Whereas sizeof(arr), would return the number of bytes, that we used to find the length of the other arrays. 

`#define ARRAY_SIZE(arr) (sizeof(arr)/sizeof(arr[0]));`

Here is another example of the difference between the two, however, the idea of the same named function, having to be worked in different ways, to get the same result: 
```
std::array<int,10> b;
    int arr[10];
    auto beg = b.begin();
    auto beg2 = begin(arr)
```

## Iterators
Iterating through an array;
**Knowing the size**: 
```
for (int i = 0; i < 10; i++){ 
	b[i] = something();
}
```

Or we scrutinise that information out in the condition: 
**Scrutinise the information**: 
```
for (int i = 0; i < b.size(); i++){
	b[i] = somethingelse();
}
```

**Iterators**: 
```
for(std::array<int,10>::iterator i = b.begin(); i != b.end(); ++i){ 
	*i = somethingfurther();
}
```

```
std::array<int, 10> b;
    int cnt = 0;
	
    for(std::array<int,10>::iterator beg = b.begin(); beg != b.end(); beg++, cnt++){
        *beg = cnt;
    }
	
    for(auto v : b){
        cout << v << " :: ";
    }

    cout << endl;
```

`#include <algorithm>` has some really cool functions that we can use on containers. 

`std::fill(b.begin(), b.end(), something());`

And we can use initializer lists: 
`b = {1,2,3,4,5,6,7,8}` etc. 

Suffering from success, as we need to know the size of them. 


## Vector 
No x or y here: 
`std::vector<int>` A pointer to represent a start. 

Contiguous data as well. 

Dynamic size, so when we run out of size in a block, it identifies that we run out of space, so we move the entire vector, so that we can add space. 

We can't assume that any pointers to a vector that has had elements added to it, are still legitimate and valid for that element that you want. Unless we know that the vector has had no more elements added to it. 

`const int* pAddressofOriginalItemZero = &(*continer.begin())`

So when we add elements to a vector, we might find that the vector will move around in memory, pointers to vector should only be init when the vector is going to remain stationary throughout our movement and use of the pointer. 

They work quite smartly, in that they will add a few elements every time, to save moving around every time something is added. Finding that space and then when you add 5 elements, it may have already allocated memory for that. 

## Lists
`std::list` A doubly linked lists. 
The idea of Nodes. 

NO GUARANTEE OF ANYTHING BEING CONTIGUOUS. 
A couple of pointers. 
One pointing to the next node, and another pointing to the node before. 

There location will not change, unlike al vector, but like an array, they are stationary in memory. 

Insertion and removal, very cheap indeed. 

The only way that we can know the size of the list, we have to go through each node and count. 

A large list, would require a large sequential counting. 
There we cannot subscript, we cannot RAM it. There is nothing contiguous, we cannot just say `list.bein() + 2`, as we have no idea what that refers to in memory, it most likely (99.999999%) of the time will not be pointing to what you want. 

Remember that the idea of subscripting is part of a calculation that requires the container to contiguous. 

Useful if we are adding to and from, but do not require to access a specific element. 

`#include <list>`

Address of each element that never changes. 

The insertion speed is super great, however, finding an element specifically is going to be really annoying. 

However, insertion speed, but also the RAM properties of the other containers: does such a thing exist: YES

## Deque
`#include <deque> std::deque`
A linked list of arrays. 
Blocks of arrays. If we add something, then we might use an array that has already been formed, or the compiler has to decide if we create a new block and the element will be the first of a new block. 

Adding an element to the middle of the list will incur some overhead. 
The index will allow us to use arithmetic values in order to get the element that we are looking for, meaning they have quite a large memory requirement, before we've even started to use them. 

RAI = random access indexing. 

So you will see chunks of memory, that are numerically contiguous, then it will bounce to another area of memory. 
However, the creation and useability of the deque means that it works contiguously. 

## Sets
`std::set` Like with maths, a set of unique objects. 
 Implying rules on to what the container can contain. 
Will also sort the objects too. 
Therefore we would insert, not push. 

Let's say if you add 5 to a set, then 5 again, it would get rid of one. 
The addresses are always the same. 

You can have an `<unordered_set>`, exclusivity still applies. 

Insertion is faster. 

Getting rid of duplicates. 


## Map
`#include <map>` and `#include <unordered_map>`
Maps are quite different indeed.
It will require two types, key and value, like dictionary in python. 

`std::unordered_map<std::string, int> container;`
`container["one"] = 1;`

If we did something like `for (auto &i : container)`, then the type returned will be a standard pair. 

So we might say something like this: 
```
for (auto &i : container){ 
	cout << i.first << " = " << i.second;
}
```
It will store in order of the first type, so alphabetical in this case. 

