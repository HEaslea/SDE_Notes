#unorderedcontainers
[[Associative Container]]

#hashfunction
The new standard will define these. 
Rather than using a comparison to organize the elements, these containers will use a `hash function` and the key type's `==` operator. 
This is most useful when we have a key type for which there is no obvious ordering relationship among elements. 
They are also useful for applications in which the cost of maintaining elements in order is prohibitive. 

Although hashing gives better average case performance in principle, achieving good results in practice often requires a fair bit of performance testing and tweaking. 
It is usually easier (and often yields better performance) to use an ordered container. 

If the key type is inherently unordered or if performance testing reveals problems that hashing might solve. 

### Using Them
#usingunorderedcontainers
Operations that manage hashing, the unordered containers provide the same operations (`find, insert, etc.`) as the ordered containers. 
Menaing that the operations that we have used on `map` or `set`, we can use the same operations on `unordered_map` and `unordered_set`. 
For unordered versions of the containers that allow multiple keys. 
As a result, we can usually use an unordered container in place of the corresponding ordered container, and vice versa. 
As the elements are not stored in order, the output of the program that uses an unordered container (ordinarily) differ from the same program using an ordered container. 
Eg. 
We can rewrite our original word_counting program from earlier to use an `unordered_map`: 
```
// count occurrences, but the words won't be in alphabetical order
unordered_map<string, size_t> word_count; 
string word; 
while(cin >> word)
	++word_count[word]; // fetch and increment the counter
for(const auto &w : word_count) // for each element in the loop
	cout << w.first << " occurs" << w.second << ((w.second > 1)? " times" : " time") << endl;
```

The output is unlikely to be in alphabetical order. 

## Managing the Buckets
#unorderedcontainerbuckets
These types of containers are organized into buckets, which will hold zero or more elements. 
They use a hash function to map elements to buckets. 

To access an element, the container first computes the element's hash code, which tells which bucket to search. 

The container puts all of its elements with a given hash value into the same bucket. 
If the container multiple elements with the same given key, all the elements with the same key will be in the same bucket. 
As a result, the performance of an unordered container depends on the quality of its hash function and on the number and size of its buckets. 

The hash function (this shit is very cool) will always yield the same result for the same argument. 
Ideally, the has function also maps each particular value to a unique bucket. 
However, a hash function is allowed to map elements with differing keys to the same bucket. 
When a bucket holds several elements, those elements are searched sequentially to find the one we want. 

Typically, computing an elements hash code and finding its bucket is a fast operation. 
If the bucket has many elements, many comparisons are needed to find a particular element. 

The containers here will provide a bunch of functions, listed next, that let us manage the buckets. 
These members let us inquire about the state of the container and force the container to reorganize itself as needed. 

![[Pasted image 20240401030601.png]]

#### Requirements on Key Type for Unordered Containers
The type of the key must have the == operator, for the sequential part of the movement throughout a bucket. 

They also use the object of type `hash<key_type>` to generate the hash code for each element. 

The library supplies versions of the `hash` template for the built-in types, including pointers. 
It also defines `hash` for some of the library types, including `strings` and the mart pointer types that we'll show off later on. 

We can directly define unordered containers whose key is one of the built-in types, or a `string` or a `smart pointer`. 

We cannot directly define an unordered containers whose key is one of the built-in types (including pointer types), or a `string` or a smart pointer. 

However, we cannot directly define an unordered container that uses our own type for its key type. 
Unlike the containers, we cannot use that hash template directly. 
Instead, we must supply our own version of the `hash` template. We'll see how to do this later on. 

Instead of using default `hash`, we can use a strategy similar to the one we used to override the default comparison operation on keys for the ordered containers. 
If we wanted to use `Sales_data` as the key, we'll need to supply functions to replace both the == operator and to calculate a hash code. 
We'll define these functions as requirements for a hasher and `==` in order to use an unordered_map/set.
```
size_t hasher(const Sales_data &sd)
{ 
	return hash<string>()(sd.isbn());
}

bool eqOp(const Sales_data &lhs, const Sales_data &rhs)
{ 
	return lhs.isbn() == rhs.isbn();
}
```

Our `hasher` function uses an object of the library `has` of `string` type to generate a hash code from the ISBN member. 
Similarly, the `eqOp` function compares two `Sales_data` objects by comparing their `ISBN`s. 

These are really weird to initialize;

We can use these functions to define an `unordered_multiset` as follows: 
```
using SD_multiset = unordered_multiset<Sales_Data, decltype(hasher)*, #
	decltype(eqOp)*>;
// arguments are the bucket size and the pointers to the hash function and quality operator 
SD_multiset bookstore(42, hasher, eqOp);
```
To simplify it just a tad, we used a type alias for an `unordered_multiset` whose hash and equality operations have the same type as our `hasher` and `eqOp` functions. 
Using that type, we define `bookstore` passing pointers to those functions we want `bookstore` to use. 

If our class has its own`==`  operator we can override just the hash function: 
```
// use FoolHash to generate the hash code; Foo must have an == operator
unordered_set<Foo, decltype(FooHash)*> fooSet(10, FooHash);
```
We don't need to input our equality operator here either. 

We will come back to this later!


A little on hashing: 
This is the general idea; we transform data into a fixed_size value (usually an integer) that represents the original data. 
The process is done using a hash function, which takes an input and produces a hash code as an output. 
A has function in this sense is a deterministic alg that takes an input (or multiple) and will output a fixed-sized hash code as output. 
A good hash function would include: 
- Deterministic - for the same input, the hash function always produces the same output
- Fast computation - duh, should be computationally efficient. 
- Uniform Distribution - Minimalizing collisions by outputting over a large range. 

In order to hash data we have to follow a series of steps: 
We have to choose and appropriate hashing function for the type that we want to hash:
`std::hash`
Passing the data to the hash function. This could be done directly as an argument to the hash function, or via an object that overloads the `operator()` to act as a hash function object. 
 Retrieve the hash code generated by the has function. 

When we have a collision, which is hopefully not too often, then we would have to move through our elements sequentially. 
This would include chaining, where we place our element using linked lists, or open addressing probing nearby locations in the hash table until an empty slot is found.


**Hash Tables** are data structures that use hashing to efficiently store and retrieve key-value pairs. 
They consist of an array (often called buckets or slots) and a has function. The hash function maps keys to indices in the array, allowing for quick retrieval of values based on their keys. They are widely used in C++ for implementing associative containers like. 

```
int main(){ 
	std::string input = "Hello, world!";
	std::hash<std::string> hasher; // creating a hash function object for strings
	size_t hash_code = hasher(input); // compute the hash code for the string
	std::cout << "Hash Code: " << hash_code << endl;
}
```
`std::hash<std::string>` 
Here `hasher` is a callable object. 
It is a specific object for `std::string`.  


So if I did something like this: 
```
size_t h = hash<string>() ("Hello there");
```
Here `hash<string>()` will return a temporary hashing function for strings, we then call that with `("Hello there")`, Whenever you see the double `()()`, the first will probably return a temp callable object. 
Similar to lambdas in that sense.

[[BtB Templates]]
[[Specialized Templates]]