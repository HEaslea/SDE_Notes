[Cherno Video on This](https://www.youtube.com/watch?v=fbYknr-HPYE)

There is also the idea of lvalue references and rvalue references. 

```
int main(){ 
	int i = 10; // there are two parts here
}
```

`int i = 10;` Lvalue being on the left, and rvalue on the right side here, not always the case but it is here. 
We couldn't say something like `10 = i`;
`int a = i;` would be setting a lvalue to an lvalue. 

If we had something like: 
```
int GetValue() { return 10; }
int main(){
	int i = GetValue(); // GetValue() here is an rvalue
	int a = i;
}
```
We would be able to do something like this `GetValue() = 5;` 

However if we got it to send back a reference, an lvalue then we could write something like this: 
```
int &GetValue(){ 
	static int value = 10;
	return value;
}

int main(){ 
	int i = GetValue();
	GetValue() = 5;
}
```


Now if we were to do something like this: 
```
void SetValue(int value){ 
}

int main(){ 
	int i = 10;
	SetValue(i);// here we are calling it with an lvalue
	SetValue(10); // here caslling with an rvalue, 
	// the 10 will be used as an rvalue to turn into a lvalue when the call is made, and it is turned into value (an lvalue)
}
```
Here is a rule that is fairly obvious once thought about, you cannot take an lvalue reference from an rvalue, duh. 

If we had something like: 
```
void SetValue(int& value) // lvalue reference here

SetValue(10);
```
So something like this: `int &a = 10;` Not allowed. 
However, we can have a const lvalue reference to an rvalue: 
`const int& a = 10;`It's a special rule: 
```
int temp = 10; 
const int& a = temp; // this is what it does presumably
```

Therefore if we had: 
```
void SetValue(const int& value) // etc.

int main(){ 
	int i = 10;
	SetValue(i);
	SetValue(10);// this is now fine
}
```
A const lvalue reference can accept both of them. 

```
int main(){ 
	std::string firstName = "Yan";
	std::string lastName = "Chernikov";

	std::string fullName = firstName + lastName;
}
```

`firstName + lastName` here is a temporary object, meaning that the string will be lost after this expression, only saved into `fullName`. Therefore that side is an rvalue. 

```
void PrintName(std::string& name){ 
	cout << anem << endl;
}
PrintName(fullName); // is fine
PrintName(firstName + lastName); // not going to work as not const ref to lvalue in the parameter 

// we would have to have (const string& name)
```

It's more obvious that we can use an rvalue for a const reference, as that value is never going to change, therefore we can reference it. 

Now we get to the idea of rvalue reference, where we have `T&&`. Two ampersands. 

Now if we wrote something like this: 
```
void PrintName(string&& name) // etc.

PrintName(fullName); // this would be an error
PrintName(firstName + lastName); // this would be fine
```
We can't pass in an lvalue, but we can pass in a rvalue. 


If we wanted to overload depending on what we are passing in: 
```
void PrintName(const std::string& name); // notice here that we could pass in either lvalue or rvalue here

void PrintName(string&& name); // taking in only rvalues
```
If we had something like this: rvalues are going to be passed into the `string&& name` overloaded form, even though the first can take both as well. 

This is very useful with Move semantics [[std Move()]]. 

The idea here is that when we arg: 
`PrintName(firstName + lastName);` we know that the args here are going to be temporary. 

lvalues in that sense are more permanent, they have some kind of storage backing them. 
rvalues are therefore temorary, they don't have some storage backing them. 

(Lvalue - > locator value). 

(PRvalue - > not a consistent memory address)

```
int x = 10; // x being a variable and an lvalue expression
int& ref = x; // x can be boudn to an lvalue reference


S s; 
s.member = 5; // s.member is an lvalue expression
```

### Temporary Materialization
The process where the compiler will create a temporary object (a prvalue) from a glvalue (which includes lvalues and xvalues), given the context requires a prvalue. 

This means that anything expecting a prvalue, can be given a glvalue. 

When?: 
- When passing arguments by value, a temporary copy (prvalue) is creawted from the original object (glvalue). 
- Therefore, returning by value -> read the book on this one, because I think RVO and NRVO cancelled this out. 

```
struct A
{ 
	A() { std::cout << "Default Constructed\n" }
	A(const A& ) { std::cout << "Copy Constructed\n" }
};

void func(A a)
{ 
	// ...
}

int main() 
{ 
	A a; // calls default constructor
	func(a); // Calls copy constructor to create a temporary value 
}
```

```
struct B
{ 
	// same as A above
};

B creatB()
{ 
	B b; // calls default ctor
	return b; // materializes a temporary prvalue
}

int main()
{ 
	B b2 = createB(); // Copy elision may optimize away the construction here
	return 0;
}
```

