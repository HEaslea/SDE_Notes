
```
class Counter{ 
private: 
	int count; 
public: 
	Counter() : count(0) {}
	
	int operator()(){ 
		return ++count;
	}
};

int main(){ 
	Counter counter; 
	cout << counter() << counter() << counter() << endl; 
	// output 1 2 3
}
```

[[Concurrency]]