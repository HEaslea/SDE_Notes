[TheChernoVideo](https://www.youtube.com/watch?v=f3FVU-iwNuA)

The Static keyword has two meanings, depending on whether it's a variable in another translation unit. 

```
void showstat(int curr){ 
	static int nStatic; // Value of nStatic is reatained
						// between each function call
	nStatic += curr;
	cout << "nStatic is " << nStatic << endl;
}


int main(){ 
	for(int i = 0; i < 5; i++)
		showstat(i);
}
```

![[Pasted image 20240220011740.png]]

The first time the function is called, the nStatic value is initialized. However, unlike other functions, the value is retained between calls. 
The lifetime of the static value will remain for the entire lifetime of the program, not just for the time of the function. 
It remains within function scope, so if you define nStatic in main, it won't affect the value of the same name in the function. 


Static in classes is slightly different in that the class will only have one value of that static. Meaning that multiple entities of that class will not have varying values. 
![[Pasted image 20240220012344.png]]Can you see how there's one value in the class, `m_i`, and this the same for every "object". Realistically, this is used as a value that remains in that scope. 
So each object has that value attributed to it, however, the first way of initializing it: `int CMyClass::m_i = 0`, is the way that we should be thinking about it as a value. 
![[Pasted image 20240220015330.png]]

Most generally, we want to just limit whatever global variable to the current translation units. 
The term static realistically just limits that variable, class, function to the translation unit that it is in. 

Remember that static variables are no implicitly inline, therefore have to be defined outside the class itself, otherwise we run into redefinition issues. 
However, member functions are fine, as they are implicitly inline. 





