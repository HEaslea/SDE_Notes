These containers all provide fast sequential access to their elements. 
Each will provide different performance trade-offs

![[Pasted image 20240313170232.png]]


Array is a fixed-size container, the others provide flexible memory management. 
We can add or remove elements, growing and shrinking. 
The strategies that they use for storing their elements have inherent, and sometimes significant, impact on the efficiency of these operations. 

`string` and `vector` hold their elements in contiguous memory. 
It is therefore fast to compute the address of an element from its index. Adding or removing elements in the middle of these takes times; all the elements after the one inserted or removed have to be moved to maintain contiguity. Every element might have to be moved in order for this to work. 

The `list` and `forward_list` containers are designed to make  it fast to add or remove an element anywhere in the container. 
They therefore don't support random access to elements. We can access elements only by iterating through the container. The memory overhead is usually substantial. 

`deque` is more complicated. Like `string vector, it will support fast random access. 
As with the others, `deque` makes it hard  to add or remove in the middle of the list. 
However, appending or adding to the front is super fast here. So keep those ideas in mind. 

An `array` is easier to use than the built-in arrays. 
They still do have a fixed size. 
A `forward-list` is intended to be comparable to the best handwritten, singly linked list. 

For most, bar a couple odd ones, `size` is a constant-time

#### Deciding which to Use
#whichcontainertouse

> Ordinarily you should use `vector` unless there is a good reason to use another container

There are a few rules of thumb that apply to selecting which container to use: 
- Unless you have a good reason, use a `vector`
- If your program has lots of small elements and space overhead matters, don't use `list` or `forward-list`
- If we require random access to elements, use a `vector` or a `deque`
- If we need to delete or insert elements in the middle of the container, use a `list` or `forward-list`
- If we need to insert at the end or the front of the, but not the middle, then `deque
If there is a mixture, then you will have to make your mind up on which is better. 

Performance testing the application suing both containers will probably be necessary in that case, especially for many iterations using the same thing.

### Container Library Overview
#containerlibraryoverview
The operations on the container types form a kind of hierarchy: 
- some operations are provided by all container types. 

![[Pasted image 20240313173138.png]]![[Pasted image 20240313173154.png]]![[Pasted image 20240313173654.png]]

In general, each container is defined in a header file with the same name as the type. 

**The containers are class templates**, meaning that they can take in any type for their constructors etc. 
Like we have seen with vectors, we must supply additional information to generate a particular container type. 
For most, but not all, we must supply the element type. 

```
list<Sales_data> // list that holds Sales_data objects
```

### Constraints on Types that a Container Can Hold
Almost any type can be used as the element type of a sequential container.
eg. we can template a container inside another container. 
`vector<vector<string>> lines; // a vector of vectors containg strings`

Some containers do impose a limitation on the types that container can hold. 

The sequential container constructor that takes a size argument uses the element type's default constructor. 
Some classes do not have a default constructor. 
We can define a container that holds objects of such types, but we cannot construct such containers using only an element count: 
```
// assume noDefault is a type witout a default constructor
vector<noDefault> v1(10,init); // fine as element initializer supplied
vector<noDefault> v1(10); // error as we must supply an element initializer
```

So every element must have an initializer, therefore a default constructor.

```
class noDefault{
public:
    noDefault() = delete;
};

int main(){
    vector<noDefault> v1(10);
}
```

This is demonstrated here. 


However: 
```
class noDefault{
public:
    noDefault() = default;
};

int main(){
    vector<noDefault> v1(10);
}
```
This would be fine. 

This is another way of thinking about it
```
class JustDefault{
public:
	JustDefault() {cout << "Just Defined an Object:)" << endl;}
};

int main(){ 
	vector<JustDefault> v1(10);
}
```
Then `"Just Defined an Object"` will be cout'd 10 times!

[[Iterators]]
[[Container Type Members]]