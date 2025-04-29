Thinking about what we need here, if we are given `n = 2`, then we are removing the second to last node (not 0 indexed).
We could just go to the end, and then go back by `n`. 
This means that we could do this recursively, which does this naturally. 

There is another approach that's worth thinking about. 

### Two Pass Approach
We traverse the entire thing and get the total length `L`. 

Say that we have this list `[0, 1, 2, 3, 4, 5, 6]` and we want to remove `n = 2`. 
This means that we have to remove `[5]` -> `L = 7` as there are 7 nodes. 
Node `[5]` is the 5th node in there. 
Therefore we are removing `L - n` - the 5th node. 
Remember, however, as is the way with linked lists - that we want to get to the node before the one we are deleting in order to have that `next` pointer go to the `next` of that `next`. As in skip past the next node. 

The idea here: 
```
// Two Pass Method

ListNode* removeNth(ListNode* head, int n)
{
	int L{};
	ListNode* p = head;
	while(p)
	{ 
		L++;
		p = p->next;
	}

	if(n > L) return head;

	// having to create a Node before head for previous to work
	ListNode dummy(0); 
	dummy.next = head; // one before the head
	ListNode* prev = &dummy;

	// second pass : stop at (L - n)th node (0 - based)
	for(int i = 0; i < L - n; ++i)
	{ 
		prev = prev->next;
	}
	ListNode* toDelete = prev->next;
	prev->next = toDelete->next;
	delete toDelete;
	
	return head;
}
```

My code :: written similarly :
```
ListNode* removeNthFromEnd(ListNode* head, int n)
{ 
	int L(getLength(head));
	std::cout << "Length: " << L << std::endl;
	
	if(n > L || n <= 0)
		return head;

	// simplify head removal and create a prev
	ListNode d(0);
	d.next = head;
	ListNode* prev = &d;

	std::size_t steps = L - n;
	for(std::size_t i = 0; i < steps; ++i)
		prev = prev->next; // advance previous

	// removing that node
	ListNode* toDel = prev->next; // n - > n + 1 (to remove) - > n + 2
	prev->next = toDel->next;
	
	return d.next; // this should just be head pointer
}
```

### One Pass Removal
What do we know about the the node that is to be removed, in comparison to the end of the list. 
Well obviously, there are `n - 1` nodes between the one to remove and `L`. 
This means that as we move forward, we can advance a front pointer by `n` steps, and then start the other at that point as well. 
So advance the front pointer `n` times, then move them both, and that means that we will automatically be that far apart. 

Again, we will need a `prev Node`  in order to remove the `head` if need be and also to have a previous node.

The thing to remember is that

```
ListNode* removeNth(ListNode* head, int n)
{ 
	ListNode d(0);
	d.next = head;
	ListNode* first = &d, *second = &d; // Start them both from this previous node

	// advancing first one, stopping early fi we hit null (edge case)
	for(int i = 0; i <= n; ++i)
	{ 
		if(!first)
			// n is larger than the length of the list
			return head;
		first = first->next;
	}
	// now move both pointers until first runs off the end
	while(first)
	{ 
		first = first->next;
		second = second->next;
	}
	// now we have second -> second + 1 (to delete) -> ... -> first (end of list, nullptr)
	ListNode* toDelete = second->next;
	if(toDelete)
	{ 
		second->next = toDelete->next;
		// delete toDelete
	}
	
	return d.next;
}
```

### Recursive
The idea being here that we have implicit backtracking. 
If we are on the last node (that `next == nullptr`), then going back `n` times will give us the node before the one that we want to delete. 
That is exactly what we want, so going back is `++` then when we are at `N == n` then we know to do the whole deleting the next one. 

```
int removeRec(ListNode* node, int n)
{ 
	// when we are N == n, then we need to remove the next node
	if(!node) // when we are one past, return 0, so that the n = 1 is that last node
		return 0; 

	int dist = removeRec(node->next, n) + 1; // return + 1
		
	if(dist == n + 1) // remove, same as the above ones
	{ 
		ListNode* toDelete = node->next;
		node->next = toDelete->next;
		// delete toDelete
	}
	
	return dist;
}

ListNode* removeNth(ListNode* head, int n)
{ 
	ListNode d(0);
	d.next = head;

	removeRec(&d, n);

	return d.next; // this is a pointer
}
```

The thing that I always forget is the idea of dereferencing: the whole `->` idea, this just means that we have a point and we are getting to the actual thing behind the pointer. 

I do love this one. 



