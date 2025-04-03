Heaps are essentially binary trees. 
The nodes are filled from left to right.

Heaps - the heap property, that is, if we have a maxheap, then that means that the highest value node is at the top. This means that if we just have the root node, and it is a minheap, say that value is 100, then we add another at value 50, then the root node is changed, and 100 becomes the child. The idea is, whether we have min or max, that the children nodes are not more maxxed or more minned than the parent nodes. 
You can see here, that 19 is actually greater than 15, however, it was the order that they were entered in I believe. 


![[Pasted image 20240527034512.png]]
![[Pasted image 20240527034922.png]]
From the visualization, you just add a new node to the available space, and check to see if you need to swap them around after that. 