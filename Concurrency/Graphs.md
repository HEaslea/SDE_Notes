The degree of a node is the number of neighbours that it has. For directional graphs, there is indegrees and outdegrees (number of outward paths and inward paths). 

**Complete Graph** - each vertex is connected to every other vertex. 

**Bipartite** - when we can colour a whole graph with 2 colours, so that no two adjacent nodes are the same colour. These are possible if a graph does not have a cycle with an odd number of edges, cycles of even numbers are fine of course. 

##### Representation
**Adjacency Lists**: - Each index is the node we are looking at, and the linked list found at that index, are all the neighbours that are for that node. 

**Adjacency Matrix**: - Very easy to show direction and weight etc. just a 2d matrix thing. `adj[a][b]` will show if there's a connection from a -> b. 

**Edge List**: - showing all edges in a certain order, great to just get through all the edges.
Put simply: we have a `vector<pair<int, int>> edges;` and then we can say like this: `edges.push_back({1, 2});` to show that we have an edge between 1, 2. 
if we need the weight as well, which is just `tuple<int, int, int>`. 

### Graph Traversal
Where like 99% of this will be. 
BFS and DFS -> which changes the order of the nodes that we are visiting. 

