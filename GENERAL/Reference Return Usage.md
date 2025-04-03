Returning a reference is not like returning a pointer, if we say something like
`int f = c.front()`, assuming that `c` is a container with `int` element types, then it will return a copy, remember that we should not be returning a reference to a local variable. 
This example will copy the front element of the container. This doesn't seem clear, as so far we have seen the idea of `int f = getInt();`
However, if you look more clearly, you will see that `getInt();` will get a value and then `f` will be a copy of that value. If `getInt()` returns  a reference, it's still just an int, with a value somewhere, therefore `f` will just copy it.
However, if we declare `int &f = getInt();` here we are explicitly saying that f is a reference to the same value. 
`getInt()` returns a reference, the lvalue itself, be careful here remember, yet if we declare `f`, then we are copying, the return type just means that we aren't returning by copy. 
Don't worry so much about whether non-reference return types return a copy or an original, it doesn't matter, however, if we return a reference, we can actively use this. 
Here if we say `int &rf = c.front();` then we are saying that `rf` is a synonym for the first element in `c`.


#returningbyreference
### Uses

1. Returning a reference to a member variable or element of an object
```
class Test{ 
public: 
	int& getValue() { return value; }
private: 
	int value;
};

int main(){ 
	MyClass obj;
	obj.getValue() = 42; // Modify the value directly through
	//  the reference
}
```

2. Returning a reference to a dynamically allocated object: 
```
int& createInt(){ 
	int* ptr = new int(42);
	return *ptr; 
}
// this is true to unique pointers as well 

int main(){ 
	int& ref = createInt();
	// use ref to access the dynamically allocated integer
	delete &ref; 
}
```
Remember that using references does not copy anything.


```
class Test {
public: 
	int value; 

	Test() : value(0) {}
	Test(int a) : value(a) {}
	Test(Test& a) : value(a.value) { printf("Copy Constructor\n"); }

	int& getValue() { return value; }
};

Test& getMyTest() {
	static Test t(10);
	return t;
}

int main(){ 
	
	Test copiedt = getMyTest(); // will output copy constructor
	copiedt.value = 42; 
	getMyTest().value = 55;
	cout << copiedt.value << "    original:   " << getMyTest().value << endl;
	// output will be 42    original: 55


	Test& rT = getMyTest();
	cout << rT.value << endl;
	// output here is 55
```


[A Good Stack Exchange](https://stackoverflow.com/questions/752658/is-the-practice-of-returning-a-c-reference-variable-evil)

Do not return a reference to a local variable: 
```
int& getInt(){ 
	int i = 10;
	return i; // DON'T DO THIS
}
```

Also don't do this: 
```
int& getInt(){ 
	int* pi = new int;
	return *pi; // ALSO DON'T DO THIS
}
```
As at some point we will have to do something like this: 
```
int& getInt = getInt(); 
delete &myInt; // must delete, this thing is weird
```
Notice that `getInt` still exists in the heap somewhere. 
```
int oops = getInt(); 
delete &oops; // undefined, wrongly deleting a copy
// that isn't on the heap
// not deleting the original
```

If we want to allocate something beyond the scope of a function, use a smart pointer (or in general a container): 

```
std::unique_ptr<int> getInt(){ 
	return std::make_unique<int>(0);
}
```

Now we can store the pointer
`std::unique_ptr<int> x = getInt();`

If we know that the lifetime of that object that we are referencing to, then that's fine, we can send back a reference to it. 

Here; to clarify; there's a copy of the pointer created at the call `getInt()`, so there is a temporary object there. The `unique_ptr` `operator=` uses its own move semantics in order to transfer over to`x`, so that we don't have more than one user of that pointer at a time. 
Now that we have to rvalue reference there, as it is a temporary object, we can use the `=operator`. 
However, we cannot do something like this: 
```
std::unique_ptr<int> x = std::make_unique<int>(10);
auto f = x; // this is copy initialization
// it is a deleted function within the unique_ptr class
```


If we know that the int will be around for a long time, longer than our reference might be used: 
```
struct immutableInt{ 
	immutableInt(int i) : i_(i) {}

	const int& get() const { return i_; }
private: 
	int i_;
};
```
