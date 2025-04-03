#vectors
Vectors are templates, NOT TYPES. 

Therefore they require the `<type>`, to let the compiler know which type the vector will be containing and acquiring memory that is necessary. 

They will hold objects, not references. 

`vector<vector<string>> file; // vector whose elements are vectors, whose elements are strings`

![[Pasted image 20240210152145.png]]

#### Using push_back() to add elements to a vector
```
vector<int> v2; 
for(int i = 0; i!=100; ++i){ 
	v2.push_back(i);}

```

Vectors grow efficiently, meaning that we do not need to create a vector at a specific size. 
If different values are required in the vector, then it would be better to add the elements to an empty vector, not one with 0 initialized types. 

This does mean that we cannot use a range `for` if we are adding to a vector. So we can't do `for(auto &i : v1){push_back()}` etc etc. 

Overall the body of the range for loop should not change the size of the holder that we are iterating over. 


![[Pasted image 20240210165850.png]]

```
vector<int>::size_type // ok
vector::size_type // error
```
