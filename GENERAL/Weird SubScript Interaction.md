We know that `arr[i]` will access an element of array, but have you thought about it like this: 

```
int main(){ 
	int arr[5] = {1, 2, 3, 4, 5};
	for(int i = 0; i < 5; ++i){ 
		printf("%d", arr[i]);
	}
	printf("\n\n");
	for(int i = 0; i < 5; ++i){ 
		printf("%d", i[arr]);
	}
}
```

This works because when we use subscript in this case, to out an element, it actually transfers to code that looks like this. 

The `arr[i]` is actually just equated to this `(*((arr)+(i)))` we have a pointer `arr` that points to the front element of the array. Then we add `i` to it. 
Therefore, due to addition within brackets, we can mutate by association and get the same thing if we do `(*((i)+(arr)))` They are exactly the same. 
It is the least readable thing that you can do, however, it might be useful one day? 


