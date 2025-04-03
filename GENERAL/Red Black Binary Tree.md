These are self-balancing binary search trees: 
Search `log n`
Insert `log n`
Delete `log n` duh
Each node has an additional attribute: a colour, either red or black. 
The root node is always black. 

Red nodes cannot have red children. 
Every path from a node to its descendant null nodes (leaves) has the same number of black nodes. 
![[Pasted image 20241030175501.png]]

The right image ensures that every path from the root to a leaf node has the same number of black nodes. 
Black nodes can have black children. 

