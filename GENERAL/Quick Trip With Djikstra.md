```
Edge* adj_list[100];

    int i;

    for(i = 0; i < 100; ++i)

        adj_list[i] = nullptr;

    Edge * e;
    for(i = 0; i < 100; ++i)
    {
        scanf("%d%d%d", &from_cell, &to_cell, length):
        e = malloc(sizeof(Edge));

        if(e == NULL)
        {
            cout << "ERROR" << endl;
        }
        e->to_cell = to_cell;
        e->length = length;

        e-> next = adj_list[from_cell];
        adj_list[from_cell] = e;
    }
```

The last two lines are the most confusing for me, `e->next = adj_list[from_cell];`
This will make the current edge point to the head of the list. 
Adding a new edge means that we have to insert it at the head of the list. 
The idea is that when we enter a new node, we have it point to the head of the list, then we change the head to the new node, so that the new head is pointing to the old head. 
Which works great. 

Obviously to avoid the jumping around in memory, we might just want to do this on the stack. 
However, this method using linked lists is really cool as well. 


### Modern Way
A great way of writing that out now in modern C++: 
```
std::unordered_map<int, std::vector<std::pair<int, int>>> graph;
// graph[node] -> { (neighbour, weight), (neighbour2, weight2), ...};

std::unordered_map<int, int> dist; // for hashing
// storing the minimum distance to each node here
```

Which just seems so simple and intuitive, while keeping it readable. 
