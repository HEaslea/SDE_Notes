Takes two operands, evaluates from left to right. 

An example here would be: 
```
vector<int>::size_type cnt = ivec.size(); 
for(vector<int>::size_type ix = 0; ix != ivec.size(); ++ix, --cnt){ 
	ivec[ix] = cnt; 
}
```
The comma allows for the both incrementing of `ix` and decrementing `cnt` at the same loop header time. 

```
typedef vector<int> VI;
```
![[Pasted image 20240219110644.png]]

This is what the the code looks like. And the output is just 5 4 3 2 1.
```
constexpr int size = 5; 
int ia[size] = {1,2,3,4,5}; 
for(int *ptr = ia, ix = 0; ix != size && ptr != ia + size; ++ix, ++ptr){ 
}
```
Here is another example where we init ptr and ix, and have to increment both at the end of our for loop header.

![[Pasted image 20240219111308.png]]
Here is a cool use of the conditional, for both x and y, using commas to appropriate the identifying portion of the expression. 

Looks very python - like here. 