#fowardlistoperations
The singly linked list, think about how the operations would work. 

Removing an element, we are changing the links in the sequence. 
![[Pasted image 20240317165623.png]]
Whenever we add or remove an element we need to access it predecessor in order to update that element's links. 
`forward_list` is a singly linked list, there is no easy way to get an element's predecessor. 
The operations therefore, to add and remove, will have to work by changing the element after the given element.

As the operations do behave differently, we have members that are like this, which are super helpful for the users: 
`insert_after, emplace_after, erase_after`.
There is also an iterator that is returned when using with `before_begin`, which will return a `off-the-beginning` iterator. 
![[Pasted image 20240317174640.png]]


When we add or remove elements in `forward_list` then we have to keep track of two iterators. 
```
forward_list<int> flst = {0,1,2,3,4,5,6,7,8,9};
auto prev = flst.before_begin(); // denotes element "off the stawrt" of flst
auto curr = flst.begin(); // denotes the actual first element
while(curr != flst.end()){ // while there are still elements to process
	if(*curr  % 2 )// if the element is odd
		curr = flst.erase_after(prev) // erase it and move curr
	else { 
		prev = curr;
		++curr;
	}
}
```

`curr` denotes the element we're checking, and `prev` denotes the element before. 
We call `begin` to init `curr`, so that the first iteration checks whether the first element is even or odd. In this case, `prev` is so that we can delete, using `erase_after`, and we just increment them both when we move forward. 
`prev` at the start will be pointing to an element that doesn't exist. 

Then `erase_after` will delete the element that `curr` is pointing to. Then `curr` = the next element. Therefore `prev` needs not be changed, it's already pointing to the previous node. 
In this case, the next odd number will be the one after the element that `curr` is on. 

