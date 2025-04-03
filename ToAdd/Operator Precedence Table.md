![[Pasted image 20240219151800.png]]

Global scope idea is thus: 
```
int var = 10;
int main() { 
	int var = 50; 
	cout << ::var << endl;// outs 10
	cout << var << endl;// outs 50
}
```





