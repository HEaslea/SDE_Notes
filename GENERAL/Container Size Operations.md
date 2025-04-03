#containersizeoperations
`size` returns the number of elements

`empty` will return a bool to see if any elements
Here is another idea for this one: 
```
	array<int,0> iArray;

    if(iArray.empty()){
        cout << "array is empty" << endl;
    }

    if(iArray.end() == iArray.begin()){
        cout << "pointers are == therefore empty" << endl;
    }
```

`max_size` returns a number that is greater than or equal to the number of elements in the container of that type can contain. 

