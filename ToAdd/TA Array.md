```
int *p = &ia[2]; // p points to the elemnt indexed by 2
int j = p[1]; // p[1] is the same as *(p+1)
// meaning that p[1] is the same as ia[3]
int k = p[-2]; // is the same as ia[0]
```

The overall idea is that the subscript number can be a negative number. 

