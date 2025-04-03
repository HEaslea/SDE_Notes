We may need to interface with arrays and/or C-style strings. 
C++ offers us the ability to do this.  

Remember: 
`string s("Hello World")`

We would find issues with the following: 
`char *str = s // error; can't initialize a char* from a string`
`const char *st = s.c_str(); // this is a fine conversion`

The first is just a poor conversion; can't allow it. 

![[Pasted image 20240216191509.png]]

Like with other `c-style strings`, this would output the entire "Hello World". It's a fine conversion. str is a pointer to an array of char, with a null termination character. The `const` means that we cannot change any of the elements of the array (string). 

Remember that we cannot initialize an array from another. 
![[Pasted image 20240216191818.png]]Like this. 

However, remember that we can use an array to initialize a vector, with the idea of the template of vector, can actually utilise the array for vector initialization. 

```
int int_arr[] = {0,1,2,3,4,5,6}; 
// ivec has 6 elements; each is a copy of the corresponding elelment in int_arr
vector<int> ivec(begin(int_arr), end(int_arr));
```
Basically initializing a vector stating a range of array to use. 
Of course you can use the idea of `begin(int_arr), begin(int_arr) + 5`

Such as: 
![[Pasted image 20240216192545.png]]

Remember that end() will pass the one past the end, making the initialization of the vector like range in python, where the second is non-inclusive. 

![[Pasted image 20240216193130.png]]

Weirdly, arrays and pointers are error prone. 