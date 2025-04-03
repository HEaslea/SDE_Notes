Wondering why we even need the friend on
```
friend std::ostream& operator<<(std::ostream& os, const MyBool& obj)
{ 
	os << (something);
	return os
}
```
This is because we are saying, using the friend definition, that we want this to be mainly defined outside the class. 

The operators that are defined in the class, will be taking an instance of the class as the LHS value: 
We can't do this with the `::ostreawm`. As then we don't have the `ostream` on the left hand side of the `<<`. 
Meaning, something we haven't really thought about is that we can overload operators just in the global name space. 
Remember ADL (argument dependent lookup) as well, that if we have args that are a particular type, then we will also be looking in their classes as well, not something we really have to think about, another compiler upgrade for us, making our lives easier. 

#### Member Function Overloads
```
class MyClass
{ 
public:
	MyClass operator+ (const MyClass& RHS)
	{ 
		...
		return MyClass();
	}
};

MyClass obj1, obj2;
MyClass result = obj1 + obj2;
```
The function implicitly is taking obj1 as this here. 

If we wanted to overload this outside of the class: 
```
MyClass operator+(const MyClass& lhs, const MyClass& rhs)
{ 
	...
	return MyClass();
}
```

Here we have to explicitly take the two args, lhs and rhs. 

```
class MyClass
{ 
private: 
	int value;
public: 
	MyClass(int v) : value(v) {}

	MyClass operator+(const MyClass& other) 
	{ 
		this->value += other.value;
		return *this;
	}
};

MyClass operator+(const MyClass& lhs, const MyClass& rhs)
{ 
	return MyClass(lhs.value + rhs.value);
}

```
