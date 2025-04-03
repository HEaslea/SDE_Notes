`break, continue, goto`

### Break
Terminates the nearest closing while, do while, for , or switch statement. 
Can only appear in an iteration statement, or switch statement. 

### Continue
Terminates the current iteration of the nearest enclosing loop and immediately begins the next iteration. Can only appear in for, while, or do while lops, including statements or blocks nested inside such loops. 

```
do{

        cout << "Please enter two values: ";

        cin >> val1 >> val2;

        cout << "The sum of "  << val1 << " : " << val2 << "=" << val1 + val2 << "\n\n";

        cin >> rsp;

    }while(!rsp.empty() && rsp[0] != 'n');
```
Very basic example printing out only odd numbers. 

```
string buf; 
while( cin >> buf && !buf.empty()){
	if (buf[0] != '_')
		continue;}
	// do something if the string starts with the _
}
```

### `goto` Statement
Programs shouldn't really be using these, they make things harder to read. 

The idea of labeled statements. 

`end: return;` 
Label identifiers are independent of names used for variables and other identifiers. 

Like with the switch statement, we cannot transfer control from a pointer where an initialized variable is out of scope to a pointer where that variables is in scope: 

```
	goto end; 
	int ix = 10; 
end: 
	ix = 42; Cannot do this at all
```

Jumping backwards to re-initialize a variable is fine however: 
```
begin: 
	int sz = get_size(); 
	if (sz < = 0){
		goto begin; 
	}
```
The variable is just reconstructed. 