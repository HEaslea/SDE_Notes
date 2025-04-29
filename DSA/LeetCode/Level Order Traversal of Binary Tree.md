## BFS

Of course, this is a BFS solution, that makes the most amount of sense :). 
That means that we are using a queue. 

There is a really neat trick with this one, that we haven't thought about before. 

#### The Neat Trick
So we are going to do this with a queue, therefore, at every level we are going to be adding the next level to the end of the queue. 
This means that if we take the queue length at the end of adding, we have the number of nodes that are on that level. 
There are two ways to go through this queue. 
We say `while q:` which is pretty normal. 
Then we can say, `for i in range(len(q)):` at the end of the adding, means that will just go through all the nodes that are on that level, and we can add at the same time. 

This is really only true for trees in general, just because we are going through all the nodes of that node, and we can get the length at the end of that. 

In this problem, we have to send back a `List[List[int]]` -> this seems annoying at first, the first time that I did this I thought `list.append(Curnode)`, but how would we know if we need to add `[]` to the list in the first place, that's really awkward. 
However, now that we know that we are just going to loop through the part of the `q` that is that level, we can just make a new `[]` and then append all those nodes. 

#### Putting It All Together

```
class Solution:
	def levelOrder(self, root:Optional[TreeNode])-> List[List[int]]: 
		toRet = []
		
		if not root:
			return toRet

		q = [] # this will be a list acting as a queue
		q.append(root)

		while q: 
			sameLevel = []
			
			for _ in range(len(q)):  # for all nodes on this level
				curNode = q.pop(0)
				sameLevel.append(curNode.val)
				
				if curNode.left: 
					q.append(node.left)
				
				if curNode.right: 
					q.append(node.right)
					
			toRet.append(sameLevel)	
		return toRet
```

#### Bottom - Up
This is super easy, as we just add to the `toRet` return List. 

All that is, is changing `toRet.append(sameLevel)` -> `toRet.insert(0, sameLevel)`. 

