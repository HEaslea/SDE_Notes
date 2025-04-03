The main property of many algorithms is the list of operations that it requires from its iterator(s). 
eg. `find` require only the ability to access an element through the iterator, then to increment the iterator, and to compare two iterators for equality (I guess "that's all"). 
Others, like `sort` require the ability to read and write and randomly access elements. 

The iterator operations, required by algorithms are grouped into five iterator categories listed here. 
![[Pasted image 20240326171023.png]]There are conventions, meaning that are *rules* or protocols into how they areused so that we don't have to learn something completely new every time we use an algorithm. 

### The Five Iterator Categories 
#iteratorcategories
Iterators define a common set of operations. 
Some are provided by all iterators; other are supported only by specific kinds of iterators. 
`ostream_iterator` s have only increment, dereference and assignment. 
Other containers will have a bunch of other operations as well. 
They are therefore categorize by the operations they provide and the categories form a sort of hierarchy. 
The iterators of the higher category provide all the operations that the iterators of a lower category will provide.

This standard specifies the minimum category for each iterator paramter of the generic and numeric algorithms. 
eg. `find` which implements a one-pass, read-only traversal over a sequence - minimally requires an input iterator. 
`replace` function requires a pair of iterators that are at least forward iterators. Its third iterator, which represents a destination, must be at least an output iterator, and so on. For each parameter, the iterator must be at least as powerful as the stipulated minimum. 
Passing an iterator of a lesser power is an error. 

### Iterator Categories
#### Input Iterators
#inputcategories

Can read elements in a sequence; 
An input iterator must provide: 
- Equality and inequality operators (==, !=) to compare two iterators
- Prefix and Postfix increment (++) to advance the iterator
- Dereference operator(`*`) to read an element: this may appear on the right-hand side of an assignment.  
- The arrow operator (->) as a synonym for `(*it).member` that is dereference the iterator and fetch a member from the underlying object. 
Input iterators may only be used sequentially. 
We are guaranteed that `*it++` is valid, but incrementing an input iterator may invalidate all other iterators into the stream. 
They may only be used for single-pass algorithms. 

#### Output Iterators
Can be thought of as having complementary functionality to input iterators. 
Output iterators must provide: 
- Prefix and postfix  increment (++) to advance the iterator
- Dereference (`*`), which may appear only as the left-hand side of an assignment. 

We may assign to a given value of an output iterator only once. 
Like inputs, output iterators may only be used for single_pass algorithms. 
Those that are used as a destination are typically output iterators.
The third parameter to `copy` is an output iterator. 
The `ostream_iterator` type is an output iterator. 


#### Forward Iterators
Can read and write a given sequence. 
They move in only one direction through the sequence. 
Forward iterators support all the operations of both input iterators and output iterators. 
Moreover, they read or write the same element multiple times. 
We can use the saved state of a forward iterator. 
Algorithms that use these may make multiple passes through the sequence. 
The `replace` algorithm requires a forward iterator; iterators on `forward_list` are forward iterators. 

#### Bidirectional Iterators
Can read and write a sequence forward or backward. 
They support all the operations of a forward iterator, these iterators will also support a postfix decrement (--). 
The reverse algorithm will require this.
Library containers will supply these types of iterators. 

#### Random-Access Iterators
They provide constant-time access to any position in the sequence. 
These iterators support all the functionality of bidirectional, as we mentioned earlier, as we move up the hierarchy, iterators will all support the lower level's operations. 

- The relational operators (< , <=, >, and >=) to compare the relative positions of two iterators. 
- Addition and subtraction operators (+, +=, -, and -=) on an iterator and an integral value. The result is the iterator advanced (or retreated) the integral number of elements within the sequence. 
- The subtraction operator (-) when applied to two iterators, which yields the distance between two iterators. 
- The subscript operator (`iter[n]`) as a synonym for `*(iter + n)`.
The `sort` algorithms require random-access iterators. 
Iterators for `array, deque, string, vector` are all random-access iterators, as are pointers when we used to access element of a built-in array. 

### Algorithm Parameter Patterns
#algorithmparameterpatterns
Understanding this parameter convention can aid in using and learning new algorithms. 
By knowing what the parameters mean, you can concentrate on understanding the operation the algorithm performs: Most of the algs have one of the following four forms: 
```
alg(beg, end, other args);
alg(beg, end, dest, other args);
alg(beg, end, beg2, other args);
alg(beg, end, beg2, end2, other args);
```
Almost all of them will take an input range, the `beg` and `end`. 
Some other algs will take values, depending on what the alg does. 

#### Algorithms with Single Destination Iterator
A `dest` parameter is an iterator that denotes a destination in which the algorithm can write its output (therefore we need an output iterator at least). 
The alg *assumes* that it is safe to write as many elements as needed, it won't check to see if there is space, that is left to us. 
If `dest` iterator refers directly to a container, then the alg will write its output  to existing elements within the container. 
`dest` is bound to an insert iterator, or an `ostream_iterator`, which will output to a stream, as long as their are no issues to writing them and there is room. 

#### Second Input Sequence
Either using `beg2` or `beg2` and `end2` use those iterators to denote a second input rjange. 
These use the elements from the second range with the input range to perform a computation.
If there is only `beg2`, then we assume that that range, as at least as large as the one denoted by `beg`, `end`.

### Naming Conventions
Separate from the parameter conventions, the algs also conform to a set of naming and overload conventions. 
These conventions deal with how we supply an operation to use in place of the default < or == operator and with whether the alg writes to its input sequence or to a separate destination. 

##### Some algs Use Overloading to Pass a Predicate
Using a predicate instead of the `<` or the `==` operator, and that do not take other arguments, typically are overloaded. 
One version taking the function uses the element type's operator to compare elements. 
```
unique(beg, end); // uses the == operator to compare the elements 
unique(beg, end, comp); // uses comp to compare the elements
```
Both calls reorder the given sequence by removing adjacent duplicated elements. The first uses the element type's == operator to check for duplicates; 
There is no ambiguity of which function is called, based on how many arguments there are between the two; 

### Algorithms with `_if` Versions
Algs that take an element value, typically have a second named (not overloaded) version that takes a predicate in place of the value. 
The algs that take a predicate are the ones that have the suffix `_if` appended: 
```
find(beg, end, val); // find the first instance of val in the input range
find_if(beg, end, pred); // find the first instance for which pred is true
```
They are basically the same, we can just plug into the second one whatever function that we want in order to change the finding thing. It will look for a value for which `pred` return as nonzero value. 

These algs provide a named version rather than an overloaded one, as both version of the alg take the same number of arguments, so we can't just overload them like that. 
Overloading ambiguities, would be possible, if they were named the same thing. 

#### Distinguishing Versions that Copy from Those that DO NOT 
Algs that rearrange elements write the rearrange elements back into the given input range. 
These provide a second version that writes to a specific output destination. 
Algs that write to a second destination append `_copy` to their names; 
```
reverse(beg, end); // reverse the elements in the input range
reverse_copy(beg, end, dest); // copy elements in reverse order into dest
```

Some algs will provide both a `_copy` and `_if` versions. 
Taking a destination iterator and a predicate: 
```
// removes the odd elements from v1
remove_if(v1.begin(), v1.end(), 
	[](int i){ return i % 2;});
// copies only the even elements from v1 to v2; v1 is unchanged
// both of them int he same function
remove_copy_if(v1.begin(), v1.end(), back_inserter(v2),
	[](int i){ return i % 2;});
```

They both use a lambda to determine whether an element is odd. 
The first, we remove the odd elements from the input sequence. 
The second, we copy the even elements from the input range into v2. 


### Container Specific Algorithms
List types will define their own versions of `sort`, `merge, remove, reverse and unique`. 
The generic version of `sort` will random access iterators. 
`sort` cannot be used with `list` and `forward_list`. 
A list can swap their elements by changing the links among its elements, rather than swapping the values of those elements. 

As a result, the list-specific version of these algorithms can achieve much better performance than the corresponding generic versions. 

![[Pasted image 20240327010221.png]]These are the `list` operations. They all return `void`.

These should be used over algorithms. 

#### `splice` Members
![[Pasted image 20240327010516.png]]

Most of the list specific algorithms are similar - but not identical - to their generic counterparts. 

The list versions will change the underlying container. 
The list version of `remove` removes the indicated elements. 
The list version of `unique` removes the second and subsequent duplicate elements. 

Similarly, `merge` and `splice` are destructive to their arguments. 
The generic version of `merge` writes the merged sequence to a given destination iterator; the two input sequences are unchanged (same as all algorithms). 
The list `merge` function destroys the given list - where elements are removed from the argument list as they are merged into the object on which `merge` was called. 
After a `merge`, the elements from both lists continue to exist, but they are all elements of the same list. 

Then there is the chapter summary :)