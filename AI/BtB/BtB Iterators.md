[[BtB]]
#btb
By our favourite man: 
[BtB Iterator video](https://www.youtube.com/watch?v=26aW6aBVpk0)


##### Looping over Array
You can use an index of the array: 
```
int arr[] = {10, 20, 30, 40};

for(int i = 0; i < 4; ++i){ 
	cout << arr[i] << "\n";
}

for(int *p = arr; p < arr+4; ++p){ 
	cout << *p << '\n';
}
```
Note that `int* p = arr+4;` is fine, but it will be undefined. 
![[Pasted image 20240403005513.png]]

Iterators are an abstraction and a generalization of pointers that Iterate: 
The general idea here is that we want to get the pointer to the first element, we can't just use the name of the thing like we do with arrays, therefore they introduced new functions to get that data. 

```
std::vector<int> v{10, 20, 30, 40, 50, 60, 70};
for(std::vector<int>::iterator pos = v.begin(); pos < v.end(); ++pos){ 
	std::cout << *pos << '\n';
}

std::string s{"Device"};
for(auot pos = s.begin(); pos<s.end(); ++pos){ 
	std::cout << *pos << '\n';
}
```
This is a great use for auto. 
![[Pasted image 20240403005932.png]]

There is still the principle that the end is not an element. 
`v.end()` is one past the end element. 
We can use `v.end()` however, the behaviour is undefined. 
![[Pasted image 20240403010047.png]]


## WHY ITERATORS
##### Index Operator vs Iterator

```
std::vector<int> vec{0, 8, 15, 47, 11, 42};
cout << vec[3]; // cheap
```
However if we used a list, we can't just compute where that element it, that is a more expensive call to get to that element. 

```
std::list<int> lst{0, 8, 15, 47, 11, 42};
cout << lst[3]; // expensive!
```

![[Pasted image 20240403010514.png]]
The basic way to deal with all elements in some data structure, is to call `begin()` and then iterate over the elements. 

If we have a template that we are going to use to iterate over a container, then the idea is that we only compile the functions for the calls that we make in the code, for the types that we call it with. 
We don't compile a generic type, we compile for functions and types that have been called in the code. 
Remember the idea of template instantiation here. 

What we are getting here is the idea that the type of iterator is very important, particularly the type that is held in the container. As the `++` operator will need to know how many bytes we have to go forward in order to get to the next element. 


![[Pasted image 20240403010954.png]]Compilers can now optimize for us; iterators are universal code most, if not all, containers that you will see. Therefore we can use a template, which is a huge optimization, in terms of writing the code itself. 

And remember that the range based for loop actually just uses the same idea of iterators. When you write one, you could also write the iterator version too!: 

![[Pasted image 20240403011158.png]]

Then you could also use `auto` here instead of a template as well. 

#### `const`ness
"It's my intention not to modify here". We can protect ourselves from ourselves to not modify something that we don't want to modify. 

There is the `const_iterator` type. 
If you don't want to type `const_iterator` every time, then we can just use `auto` with `cbegin(), cend()`. 

![[Pasted image 20240403011445.png]]

He's saying here too, that C++ wants to protect you from doing something that is so poor for performance; and that applies for not having the index operator available unless we can just jump to an element, called "random access". 

We do the same for iterators, dong somethings with iterators, there is remarkably bad performance. 

With some iterators you can do more than others. 

#### Iterators and their Categories
[[Structure of Generic Algorithms]] also talks about this as well with algorithms. 
Iterator categories support various operations that they can use. 

Hash tables use linked lists. 

![[Pasted image 20240403011920.png]]

Most (almost all) will use the `++` operator, therefore most containers can be read like this: 

But if we did something like this: 
```
for(auto pos = coll.begin(); pos < coll.end(); pos += 2){ 
	std::cout << *pos << '\n';
}
```
This will only work for those containers that support random access; `array, vector, deque`only. 
To note here as well, if you had written `pos != coll.end()` and we jump over that position with something like `pos+=100`; then we are screwed and we will just keep writing silly bits of undefined information. 

How we might write this for all containers: 
```
for(auto pos = coll.begin(); pos != coll.end(); ){ 
	cout << *pos << '\n';
	++pos; 
	if(pos != coll.end()) ++pos;
}
```


In later C++, there are more categories: 

**Contiguous range/iterator** where memory is contiguous. 
![[Pasted image 20240403012721.png]]

### STL
- Data structures as ranges
- Algorithms
- Iterators as the glue interface
![[Pasted image 20240403012844.png]]

Half-open ranges are those where begin refers to an element, and end refers to one off the end. 
**Standard Algorithms**; 
- process elements of half-open ranges
- using the Iterator Interface for element access
- Generic to operate on different container types

eg. `sort` for random access iterators, as they have to jump back and forth in order to sort. 

eg. again with `max_element` if there is no element that is found here, then it will return the `end()` position so that we know. Here if the collection is empty. 

**Algorithms are Generic** 
Using the iterator interface for all containers. 
Provided that all operations are supported. 
```
template<typename IterT, typename ValueT> 
ValueT accumulate (IterT beg, IterT end, ValueT val)
{ 
	for(IterT pos = beg; pos != end; ++pos){ 
		val = val + *pos; // add value of each element 
	}
	return val;
}
```

![[Pasted image 20240403013501.png]]
