**Adjacency Matrix**: A 2D matrix where `matrix[i][j]`, indicates that there is an edge from node `i` to `j`. 
**Adjacency List**: A `list[i]` where all the neighbours of node `i` are contained. 
**Edge List**: `[(u, v), ...]`. 

#### The Shortest Path
As we explore all options when we are aware of them. Therefore, if we track the minimum to that node, then we are going to keep the shortest path to that node all the way through. 

From the book, the general idea is that if we are visiting places, very similar to Djikstra, we are going to keep track of which current positions we want to look at. 
Then we are looking at `while (someCurrentNotion > 0)` then we are adding to this `someCurrentNotion` as we are adding new locations, eventually we run out of eligible. 

DFS would just be working on the most recent recursive element that we have found. 
However, with BFS, we want to cache all the new ideas that we have, in order to work on each one on the next turn. 
#### Graphs and the Problems Pertaining to Them
**DAG**: Directed Acyclic Graphs - Meaning that we are 



###### Bidirectional BFS
Start BFS from both the source and the target. Which is how the rubik's cube solution generator works, starts from both sides and picks the solution that meets first in the middle. 


###### 0-1 BFS
For graphs with edge weights of 0 and 1, use a deque instead of a queue. 
Push nodes with edge weight 0 to the front of the deque. 


###### Priority Queue (Djikstra)
Priority queue so that we just work with the shortest path first. 

###### Dynamic Programming on Graphs
Combine DP with graph traversal for problems like Longest Path in DAG.  


#### Tackling Harder Graph Problems
**Visualize The Problem**: 
- Draw the graph, or represent it with adjacency lists/matrices. 

DFS will have greater backtracking capabilities, given the recursive nature of the whole thing. 

The great thing with BFS is that we don't have to track the minimum, that is done for us, by way that we return from whatever we are doing when we are there, which will be the minimum.
As DFS will explore one path fully before backtracking all the way, that's why we need the minimum there. 

DJIKSTRA is a great way of thinking about bfs, adding to a list that is priority, then we just implement over the top of that. 



### The Pattern 
They all seem to follow this similar structure: 
You have a thing that you are going to look at the nodes of. 
Then you have a queue, of all the things that you want to look at, the new positions. 
Then every time we go through all the new positions, that is, going through the queue: 
`for _ in range(queue):` 
`for(int i = 0; i < queue.size(); ++i)`
Then we are going to go through each new position, and add to queue the new positions. 
However, as we are only going through the initial length of the queue, we are not going to go through all the new positions yet. 
This is done in the book by having two arrays. 
Checking each position before moving onto **that same** position, means that we are doing a BFS. 
We do what we need to for every node, we add those things that we are doing to the queue, as new positions to check. 

This is just DJIKSTRA, going through all the nodes, updating them etc. 