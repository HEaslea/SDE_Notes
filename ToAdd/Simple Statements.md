Statements executed sequentially. 

However, not always what we want, therefore we have flow-of-control statements, allowing for more complicated execution paths. 

Most statements end with a ; , making it an expression statement. 
Causing the expression to be evaluated and its result discarded. 

```
ival + 5; // rather useless expression statement
cout << ival; // useful expressions statement
```
The first statement is useless as the addition is done but the result is not taken anywhere. 

### Null Statements
This would be a single semicolon `;`. 
Useful when we logically don't need a statement but the language requires one: 
```
while(cin>>s && s!=sought){ 
;
}
```
Don't do anything with s or the input, however keep going until we find what we are looking for. 

Meaning that if we have two semicolons at the end of an expression, then that's fine, the second is just a null statement. 

`ival = v1 + 2;; // fine as second is a null statement` 

HOWEVER this would be a disaster and the loop would go indefinitely: 
```
whle(iter!=svec.end());
	++iter; // this is not part of the loop
```

### Compound Statements
Referred to as a block, a sequence of statements, and declarations surrounded by a pair of curly braces. 
A block is a scope, names introduced on the inside are accessible only in the block and blocks nested inside it. 
The body of a `while` or `for` must be a single statement, yet we often need to execute more than one statement in the loop. 
```
while(val <= 10){ 
	sum += val; 
	++val;
}
```

### Statement Scope
```
while(int i = get_num()){ 
	cout << i << endl;}
i = 0; // error i is not accessible outside the loop
```

```
auto beg = v.begin(); 
while(beg!=v.end() && *beg >= 0){ 
	++beg;}
if (beg == v.end()) // we would know that all the elements in v are +ves
```

### Conditional Statements
`if` determines flow of control based on a condition. 
`switch` will evaluate and choose flow based on the expression's value. 

##### If Statement
```
if (condition)
	 statement
else if(condition 2)
	statement2 
else
	statement3
```

`const vector<string> scores = {"F" etc.}`
```
string lettergrade;
if(grade < 60)
	lettergrade = scores[0]
else
	lettergrade = scores[(grade-50)/10];
```
Remember that we can do nested if statements too. 
Just remember to use curly braces correctly. 

### Switch Statement
```
unsigned aCnt = 0, eCnt = 0, iCnt = 0, oCnt = 0, uCnt = 0; 
char ch; 
while(cin >> ch){ 
	switch(ch){ 
		case 'a': 
			++aCnt;
			break;
			etc.etc.
	}
}
```
Break just sends flow out of the switch statement and back to the while loop. 

```
char ch = getVal(); 
int ival = 42; 
switch(ch){ 
	case 3.14: // error : noninteger as case label
	case ival: // error nonconstant as case label
}
```
Error if two case labels have the same value. 
We can fall through multiple case labels. 

```
unsigned vowelCnt = 0;
switch(ch)
{ 
	case 'a': 
	case 'e': 
	case 'i': 
	case 'o':
	case 'u': 
		++voweCnt;
		break;
}
```

This means that any occurrence of a vowel will up the vowel count. 

```
switch(ch)
{ 
	case 'a':case 'e': case 'i': case 'o': case'u': 
		++vowelCnt; 
		break;
}
```
Due to the freeform nature of C++. 
Think of it like, in the switch statement, if one case is true, then if there's no break, the switch is true and will execute all the case statements below it. 

##### Default Label
When no case label matches, then we execute the default expression. 
```
switch(ch){ 
	case 'a' : case 'e' : case 'i' : case 'o' : case 'u': 
		++vowelCnt; 
		break;
	default: 
		++otherCnt; 
		break;
}
```

Needing a variable in any particular block, you would have to use the curly braces: 

```
case ture: 
{ 
	// declaration statement within a statement block
	string file_name = get_file_name();
	// ... 
}
	break;
case false: 
	if (file_name.empty()) // error : file_name is not in scope

```



## `while` Statements

Remember that the variables within the block are created and destroyed each time round. 


```
vector<int> V; 
int i; 
// read until EOF or other input failure
while(cin >> i)
	v.push_back(i);
auto beg = v.begin();
while (beg!=v.end() && *beg >= 0)
	++beg; 
if (beg == v.end())
	// then we know that all elements in v are great than or equal to zero
```


### Traditional For Statement
`init-statement` along the lines of `for(initializer;condition;expression) { statement}`
Expression occurs after a loop. Can we either a single or a compound statement and expression. 

Execution flow in  a traditional for loop. 
```
for (decltype(s.size()) index = 0; index != s.size() && !issapce(s[index]); ++index){ 
	s[index] = toupper(s[index]); // capitalize the current character
}
```

1. init statement executed once at the start of the loop. 
2. Condition is evaluated, if false on the first iteration,. then we bypass the entire body. 
3. Execute the body. 
4. Expression is evaluated, index is incremented by 1. 

Multiple definitions in the header: 
The init-statement may only be one statement, meaning all the types must be the same type. If you wanted something to be declared and used in the loop as well. 

```
char carr[] = {'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'};

    for(int i = 0; i <= 10; ++i){

        static char* cp = carr;

        cout << i << "  " << *cp << E;

        cp++;

    }
```

[[Static Keyword]]

If you wanted to omit any part of the header, then you just use a null. 

```
for(;;){

        static int i = 0;

        cout << i << E;

        if(i == 10){

            break;

        }

        i++;

    }
```

With no condition, our body must have a way of exiting the loop, in this case `break;`


### `range for` Statement
```
for(declaration : expression){ 
}
```
Expression must be a sequence, such as a braced initializer list, or an object that has begin and end, that return iterators. 

Using auto to deduce type is very useful. 

Then accessing the actual element in the object using a reference: 

### `do while` Statement

Condition is tested after the executed block, the block will always execute at least once: 

```
do{

        cout << "Please enter two values: ";

        cin >> val1 >> val2;

        cout << "The sum of "  << val1 << " : " << val2 << "=" << val1 + val2 << "\n\n";

        cin >> rsp;

    }while(!rsp.empty() && rsp[0] != 'n');
```

Pretty cool while as well, with the idea that the first char is not n and it's not empty. 

