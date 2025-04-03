So when do my variables get copied: 
1. **Passing by Value**: A copy of the variable is made when we pass by value: 
```
void foo(int x) { 
	x = 10;
}

int main(){ 
	int value = 5;
	foo(value); // value is copied when passed to foo 
	// value sitll holds its original value(5)
return 0;
}
```


2. **Returning by Value**: A copy will be made here: 
```
int getValue(){ 
	int x = 10;
	return x;
}

int main() { 
	int value = getValue();
	return 0;
}
```

3. **Assignment Operator**: when assigning one variable to another, a copy of the variable's value is made: 
```
int main(){ 
	int x = 5; 
	int y = x; // y is assigned a copy of the value of x
	return 0;
}
```

4. **Initialization** : when init a variable with another variable's value, a copy is made: 
```
itn main(){ 
	int x = 5;
	int y(x); // y is initialized with a copy of x's value
	return 0;
}
```

### Copy Initialization
When we use this version of initialization, the compiler creates a temporary object to hold the value of the expression., then copies that value into the variable being initialized. 
```
int main(){ 
	int x = 5; // create a temporary creation of 5, then copy it as x
}
```

