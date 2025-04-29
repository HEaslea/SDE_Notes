### Position of the Recursion

```
void recOne(int n)
{ 
	if(n == 6) return;

	std::cout << "RecOne: " << n << std::endl;
	
	recOne(n + 1);
}

void recTwo(int n)
{ 
	if(n == 6) return;

	recTwo(n + 1);

	std::cout << "RecTwo: " << n << std::endl;
}

void rec(int n)
{ 
	recOne(n);
	std::cout << " -------------------------- " << std::endl;
	recTwo(n);
}
```

```
// output
RecOne: 0
RecOne: 1
RecOne: 2
RecOne: 3
RecOne: 4
RecOne: 5
 -------------------------------
RecTwo: 5
RecTwo: 4
RecTwo: 3
RecTwo: 2
RecTwo: 1
RecTwo: 0
```

This is to simulate the idea of processing within that frame stack before recursion, or after. 

