[[BtB Templates]]
#templates #specializedtemplates
Templates allow us to generalize classes or functions that can work with a whole range of data. 
Template specialized allows you to provide custom implementations for specific types or combinations of template parameters. 

This is particularly useful if the default behaviour of the function/class doesn't suit a certain type, or we require optimizations for specific classes. 

```
template<typname T> 
class MyClass{ 
	// class definition
};

template<> 
class MyClass<int>{ 
	// Specialized implementation for int...
};
```

#### Specializing a Class Template: 
```
template<typename T1, typename T2> 
class Pair{ 
public:
	Pair(T1 first, T2 second) : first_(first) , second_(second) {}

	T1 first() const { return first_;}
	T2 second() const { return second_;}

private: 
	T1 first_;
	T2 second_;
};

template<>
class Pair<int, int>{ 
public:
	Pair(int first, int second) : first_(first), second_(second) {}

	int sum() const { return first_ + seoncd_;}

private: 
	int first_;
	int second_;
};
```

#### Specializing a Function Template
```
template<typename T>
void print(T value){ 
	std::cout << value << std::endl;
}

template<> 
void print<std::string>(std:;string value){ 
	std::cout << "\"" << value << "\"" << std::endl;
}

int main(){ 
	print(42); // calls the generic version
	print("hello"); // calls the specialized version for strings
}
```

#### Specializing for Pointers
```
template<typename T>
class PointerWrapper{ 
public: 
	PointerWrapper(T* ptr) : ptr_(ptr) {}

	T& operator*() const { return *ptr;}
	T* operator->() const { return ptr_;}

private: 
	T* ptr_;
}

template<>
class PointerWrapper<int>{ 
public: 
	PointerWrapper(int* ptr) : ptr_(ptr) {}

	int& operator*() const { return *ptr_; }
	int operator*() const { return *ptr_; } // Additional Specialization
	int* operator->() const { return ptr_; }

private: 
	int* ptr_;
}
```

#### Specializing for Enums: 
```
enum class MyEnum { A, B, C };

template<typename T>
void printEnum(T value){ 
	std::cout << static_cast<int>(value) << std::endl;
}

template<> 
void printEnum<MyEnum>(MyEnum value){ 
	std::cout << "Enum value: ";
	switch(value){ 
		case MyEnum::A: std::cout << "A"; break;
		case MyEnum::B: std::cout << "B"; break;
		case MyEnum::C: std::cout << "C"; break;
	}
	std::cout << std::endl;
}

int main(){ 
	printEnum(42);
	printEnum(MyEnum::B); // calls the specialized version
}
```




