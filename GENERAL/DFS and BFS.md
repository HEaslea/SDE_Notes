# Tree Traversal

![[Pasted image 20240813145952.png]]
![[Pasted image 20240813145959.png]]Tree Traversal is the idea of visiting or accessing each node of the tree exactly once in a certain order. 

Imagine the tree like this. 

## Inorder Traversal: 
![[Pasted image 20240813150121.png]]
LEFT -> ROOT -> RIGHT

All the algs are basically the same, they just do things in a different order. 

```
void inorderTrav(Node* curr)
{ 
	// where we pass in the root node
	if(curr == nullptr)
		return;

	inorderTrav(curr->left); 

	cout << curr->data << " ";

	inorderTrave(curr->right);
}
```
Just imagine the callbacks, and how if we have a nullptr at any one point, the recursion means that we move back up the nodes to the one before. 

The main function is just adding nodes as we: 
```
int main() 
{ 
	Node* root = new Node(1);
	root->left = new Node(2);
	root->right = new Node(3);
	root->left->left = new Node(4);
	root->left->right = new Node(5);
	root->right->right = new Node(6);
```

Then just call with the inorder Traversal;

## Preorder Traversal 

ROOT->LEFT->RIGHT
Just reordering the algorithm here: 
```
void preorderTrav(Node* curr)
{ 
	if(curr == nullptr)
		return;

	cout << curr->data << " ";

	preorderTrav(curr->left);
	
	preorderTrave(curr->right);
}
```
The idea being here that we go just output the data if we are on a node: 
then we move to the left if we can, if that is nullptr, then we go back to the node before. 

Remember that we return to the point of the call in the function, therefore we are not rewriting out the data, and we go right, if we return from going left: 

Used to create a copy of the tree: 

Also for prefix ordering of expressions: remember those:  That's a good way to think about it!
## Postorder Traversal
LEFT->RIGHT->ROOT

Right never comes first: 

```
void postorderTrav(Node* curr)
{ 
	if(curr == nullptr)
		return;

	postorderTrav(curr->left);
	
	postorderTrav(curr->right);

	cout << curr->data << endl;
}
```


Very useful for getting the postorder of an expression;

# BFS 
## Level order Traversal

![[Pasted image 20240813154311.png]]
Here we can create a queue: 
- Create a queue
- Put the root node of the tree into it
- Loop while Q is not empty: 
	- Dequeue a node from the queue and visit it
	- Enqueue the left child of the dequeued node if it exists
	- Enqueue the right child of the deqeueued node if it exists

From the picture above: 
5 put on the q, then we remove 5 from the que and we add 12 and 13 to the queue. 
12 gets removed from the queue and only 7 is added [13, 7];

Then we add go to 13, add 14 and 2; 
[7, 14, 2] now; etc. etc. 

```
void levelOrderTraversal(Node* root)
{ 
	if(!root) return;
	queue<Node*> q;
	while(!q.empty())
	{ 
		Node * curr = q.front();
		q.pop();
		cout << curr->data << " ";
		if(curr->left) q.push(curr->left);
		if(curr->right) q.push(curr->right);
	}
}
```

Just seems really obvious now that I've done it. 

Then we have DFS for graphs: 

## DFS for Graphs
![[Pasted image 20240813155147.png]]

`****Input:**** V = 5, E = 5, edges = {{1, 2}, {1, 0}, {0, 2}, {2, 3}, {2, 4}}`
DFS steps here are: 
Source : 1
Go to 2: 
Go to 3: after backtracking to 2;
Go to 4; after backtracking to 2;

This is similar to Preorder Tree Traversal: ROOT -> LEFT -> RIGHT

Essentially, when we get to a node, we add its children to a stack. Then we go to the child that is first on the stack until the stack is empty. 

![[Pasted image 20240813155636.png]]
When you look at this, you can kind of see how Pre-Order will add its left and right child to the stack. We move to the left one first, then we add that node's children tot he stack, the right nodes are mostly going to be last. 
The best thing to think about with these, is whether they use a queue or a stack implementation: and in what order we get the nodes out of that. 
The main thing with this is that we don't necessarily visit the nodes in order that we come across them, we go to nodes that we have most recently seen. 
## BFS for Graph
Conversely, here we visit the nodes as we have seen them in order. we are not just visiting the most recent one we have seen. 


![[Pasted image 20240813160211.png]]![[Pasted image 20240813160213.png]]![[Pasted image 20240813160216.png]]![[Pasted image 20240813160219.png]]
etc. etc. 
Here we of course use a queue! Very cool indeed. 


### The Crucial Difference
The main difference here is that depth first will move away from the source code very quickly, provided that the first seen child node of a node is not closer to the source. Essentially, we just move to the first node seen by the node we are on. 
