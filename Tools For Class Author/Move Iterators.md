[[Moving Objects]]
This is an iterator adaptor (in the same way that we had container adaptors, for use of stack etc). 
The idea with the container adaptors, is that we are not focussing on how the data is stored, just how it is popped and pushed. 
FIFO or LIFO. 

This adaptor will behave exactly like the underlying iterator, except that dereferencing converts the value returned by the underlying iterator into an rvalue. 

If used for an input, the effect is that he values are moved from, rather than copied. 
Similar to normal iterators, the ++ and -- all work the same way. 
This is really important, in that our object that we are pointing, might not be usable the same way, however it will be valid. 

You can also compare the underlying iterators too, just like with normal iterators. 

We can do also `base` which will access the underlying iterator. 

`make_move_iterator` created a `std::move_iterator` of type inferred from the argument. 

[CPPref notations](https://en.cppreference.com/w/cpp/iterator/move_iteratorf)
```
void print(const std::string_view rem, const auto& v)
{
    for(const auto & s : v)
        cout << std::quoted(s) << ' ';
    cout << "\n";
}


int main(){
    std::vector<std::string> v{"this", "_", "is", "_", "an", "_", "example"};
    print("Old contents of the vector: ", v);
    std::string concat;
    for(auto begin = strd::make_move_iterator(v.begin()),
        end = std::make_move_iterator(v.end()); begin != end; ++begin)
        {
            std::string temp { *begin } // moved the contents of *begin to temp
            concat += temp;
        }

    print("New contents of the vector: ", v);
    print("Concatenated as string: ",std::range::single_view(concat));
```

This is pretty simple to follow:
As we are using move iterators, we know that dereferencing something here will mean that it is moved from. 

The output looks a little something like this: 
![[Pasted image 20240421005855.png]]

Ordinarily, an iterator will return an lvalue reference to the element. 
A move iterator will yield an rvalue reference. 

Using `make_move_iterator`. 
Taking an iterator and returning a move iterator. 

All of the original iterator's other operations will work just fine. 
We can pass a pair of iterators to an algorithm.
We can also pass move iterators to `unitialized_copy`. 

```
void StrVec::reallocate()
{ 
	// allocate space for twice as many elements as the current size
	auto newcapacity = size() ? 2* size() : 1;
	auto first = alloc.allocate(newcapacity);
	// move the elements

	auto last = uninitialized_copy(make_move_iterator(begin()),
		make_move_iterator(end()), 
		first); 
	free(); // free the old space
	elements = first; // update the pointers
	first_free = last;
	cap = elements + newcapacity;
}
```
`uninitialized_copy` calls `construct` on each element in the input sequence to "copy" that element in the destination. 
The dereference from these move iterators, will yield an rvalue reference, which means `construct` will use the move constructor to construct elements. 
It's worth noting that std lib makes no guarantees about which algorithms can be used with move iterators and which cannot. 
As moving an object can obliterate the source, you should pass move iterators to algorithms only when you are **confident** that the algorithm does not access an element after it has assigned to that element or passed that element to a user-defined function. 

A moved-from object will have an indeterminate state, calling `std::move` on an object is a dangerous operation. 
When we have `moved`, then we have to make sure that no other uses of the moved-from object, unless you are fine with having an indeterminant object, and unspecified behaviour. 

`move` can offer massive performance boosts. 
Outside of class implementation code, eg. move constructors, move assignment, use `std::move` only when you **are certain** that you need to do a move, and you can guarantee it to be safe. 






Example Code: 

Using iterator arithmetic: 
```
typedef vector<int> vi;

void Print(vi::iterator iter){ 
cout << *iter << endl;
}
int main(){ 
	vi v = {0,1,2,3,4,5};
	auto vBegin = v.begin();
	Print(vBegin+=2); // outputs 2
	Print(v.end() -= 2); // outputs 4
	if((v.begin() += 5) == (v.end() -=1)){ 
		cout << "The same" << endl;
	}
}
```




