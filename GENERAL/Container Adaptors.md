#adaptors
Sequential Container adaptors; `stack`, `queue and priority_queue`. 
An adaptor is a general concept in the library. 
There are container, iterator, and function adaptors. 
Essentially, an adaptor is a mechanism for making one thing act like another. 

The `stack` adaptor will take a sequential container and makes it operates as if it were a `stack`.
![[Pasted image 20240318153445.png]]
## Defining an Adaptor
Each adaptor defines two constructors: the default constructor that creates an empty object, and a constructor that takes a container and initializes the adaptor by coping the given container. 
Imagine  if we had `deq` that is a `deque<int>`, we can use `deq` to initialize a new `styack` as follows: 
```
stack<int> stdk(deq); // copies elements from deq into stk
```

We can override the default container type by naming a sequential container as a second type argument when we create the adaptor: 
```
// empty stack implemented on top of vector
stack<string, vector<string>> str_stk;
// str_stk2 is implemented on top of vector and initially holds a copy of svec
stack<string, vector<string>> str_stk2(svec);
```
There are indeed constraints on which container can be used for a given adaptor. 
All of the adaptors require the ability to add and remove elements. 
They cannot be built on top of an array. 
We cannot use a `forward_list`, all the adaptors will require operations that add, remove, or access the last element in the container. 
#stack #queue
A `stack` requires only `push_back` and `pop_back` and `back` operations (lol) in general

The `queue` adaptor requires `back, push_back, front, push_front` so they can be built on `list or deque` but not on `vector`. 
#prioryqueue
`priory_queue` requires that we have random_access in addition to the `front, push_back, and pop_back` operations; it can be built on a `vector, deque` but nont on `list`.

### Stack Adaptor
Stacks are not standalone containers, meaning that they require other containers in order to hold the objects/values
Realistically, it is just the LIFO principle. Pennies on a stack.
Defined in the `stack` header. 
The operations that we have here are provided as such. 
It adapts the interface of an existing container to provide the functionality of a stack. 

If we do something like this: 
```
std::stack<int> myStack; // without specifying the container that we are overlaying, we will just be given a deque
```
However, we can specify containers that we want to overlay: 
Remember, certain containers may be better suited for stack operations in terms of performance or memory usage. 

- **`std::deque`**: Deques are typically a good choice for implementing stacks because they support efficient insertion and deletion operations at both ends. This makes them well-suited for push and pop operations required by stacks.
    
- **`std::vector`**: Vectors provide contiguous storage, which can be beneficial for cache locality and iteration performance. However, inserting or removing elements from the middle of a vector can be less efficient compared to deque, especially for large containers.
    
- **`std::list`**: Lists provide efficient insertion and deletion operations anywhere in the container, including the front and back. While they may not have the same cache locality benefits as vectors, they can be a good choice if you frequently need to insert or delete elements in the middle of the container.

Filling up the stack: 
```
for(size_t ix = 0; ix != 10; ++ix)
	intStack.push(ix); // intStackholds 0 ... 9 inclusive
while( !intStack.empty()){ // while there are still values in intStack
	int value = intStack.top();
	// code that uses value
	intStack.pop(); // pop the top element, and repeat
}
```
![[Pasted image 20240318235945.png]]


When we overlay a container with a stack, we will only be able to use the stack operations, rather than the underlying container type operations. 

`intStack.push(ix);` this will actually call the `push_back` on the `deque` object, which is the standard in initializing a stack object. 
We would have no direct access to the `deque` operations and functions, so rather than `push_back`, we have to use `push()`. So essentially the container just becomes that, the container, it does not define any other operations, it just is the holder, and the implementation for how data is held. 
And the adaptor is how we move that data, however, the implementation remains with the container. 


### Queue Adaptors
![[Pasted image 20240319000752.png]]
Remember that queues will use a FIFO storage and retrieval policy (policy is more the right word here than anything else). 
