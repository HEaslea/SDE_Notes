```
class MyNumber{ 
private: 
	int x; 
	int y; 

public: 
	MyData(int a,  int b) : x(a), y(b) {}

	operator int() const { 
		return x; // implicitly convert to `x`
	}

	operator std::string() const{ 
		return string("Hello");
	}
};

int main(){ 
	MyData data(10, 20);
	int result = data; // Implicit Conversion from MyData to int
}
```

So you don't need to specify the return type on a conversion function: 
Remember that `const` before a member function will allow us to call this member function on `const` objects. 

```
operator int(){ 
	return m_IntValue;
}
```

```
MyData data(10, 20);
cout << (string)data << endl; // output "Hello" from the function above.
```
