
## Runner Technique
Using two pointers to iterate through the linked list at a time, they will usually be at different paces. 

Say that we want p2 to be at the end and p1 at the half: 
Imagine that we want `a1 -> a2 -> ... -> an -> b1 -> b2 -> ... -> bn` 
Yet we want `a1 -> b1 -> a2 -> b2 -> ... `
Then we could have p1 that goes 1 for p2's 2, then p1 will be at the half and p2 at the end, then we move p2 to the beginning, then weave the elements together. 

#### Removing Dupes

```
void deleteDupes(ListNode* n)
{ 
	std::unordered_set<inT> seen; // Using a Hash Table here
	ListNode* prev = nullptr; 
	while(n != null)
	{ 
		if(seen.find(n.data) != seen.end())
		{ 
			prev.next = n.next; // delete by going over that node
		} else 
		{ 
			seen.insert(n.data);
			previous = n;
		}
		n = n.next;
	}
}
```
`O(n)`

Think about doing this without the extra data set: 
We have two runners, one on the current, then one that goes through the rest, checking and deleting when we need to.
```
void deleteDupes(ListNode* head)
{
	ListNode* current = head;
	while(current != null)
	{ 
		// go through the other nodes and delete dupes
		ListNode* runner = current;
		while(runner->next != null) // while the next node still exists
		{ 
			if(runner->next->data == current.data)
			{ 
				// remember that we have to have access to the previous node, therefore, we stop on the previous node
				// now we point around this node
				runner->next = runner->next->next; // this might be a nullptr and that's fine
				// and we do not advance runner
			} else 
			{ 
				runner = runner.next;
			}
		}
		current = current->next;
	} 
}
```
`O(1)` in space 
`O(n^2)` in time
#### Return `k`Th last element
