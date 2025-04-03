Every container type supports the equality operators (== and !=). 

Most containers, except the unordered associative containers, support the relational operators (`>, >=, <, <=`). 
The right and left hand operands must be the same kind of container, holding the same type. 
eg. Comparing `vector<int> to vector<int>`

Comparing two containers performs a pairwise comparison of the elements. 
They work similar to a string comparison, which was really just comparing the integer value of the chars. eg. `H < h` as H comes before h in the integer. If a string is longer, but otherwise equal to the other, then that longer string will be `>`. 

- if both containers are same size, and all elements are equal, the two containers are equal. 
- If they are differing sizes, every element of the smaller one is equal to the corresponding element of the larger one, then the smaller one is less than the other. 
- Or we compare the first unequal element. 

![[Pasted image 20240316003619.png]]

#### Relational Operators use their Element's Relational Operator
If the element type doesn't support the required operator, then we cannot compare containers of that element type. 
If our type doesn't define ==, then how will our containers support it etc. 
This also gives us insight as to the fact that C++ will go through one by one and do that operation to the types. 
As in you wouldn't, without definition, be able to do this: 
`Sales_data A, B; if(A < B)`
Therefore: `vector<Sales_data> storeA, storeB; if(storeA < storeB)` we run into issues, the latter due to the first. 