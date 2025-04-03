```
void HelloWorld()
{ 
	std::cout << "Hello World" << std::endl;
}

int Add(int a, int b){ return a + b; }


// we might also use
typedef void(*HelloWorldFunction)();
typedef int(*AddPointer)(int, int);



int main(){

	auto function = HelloWorld; // getting the function pointer &HelloWorld is fine too

	// the memory address of the CPU instructions

	function(); // would call the function

	// the type is: void (*function)(), without a name it would be void(*)()

	void(*cherno)() = HelloWorld;

	// after typedef
	HelloWorldFunction function = HelloWorld;

	Addpointer aPtr = Add;
}
```



```
void PrintValue(int value){ 
	std::cout << value << std::endl;
}

void ForEach(const std::vector<int>& values, void(*func)(int)){ 
	for(int value : values){ 
		func(value);
	}
}

int main(){ 
	vector<int> values = {1,5,4,2,3};
	ForEach(values, PrintValue);
}
```



I really like this example where we use a type alias in the beginning: 
```
using POP = void(*)(int);

void PrintOut(int value){ 
	cout << "Value: " << value << std::endl;
}

void forEach(std::vector<int>& values, POP func){ 
	for(int val : values){ 
		func(val);
	}
}

int main(){ 
	std::vector<int> nvec = {0,1,2,3,4,5};
	forEach(nvec, PrintOut);
}
```
![[Pasted image 20240319024753.png]]

I really like the idea of `POP func`, it's so hard to read lol, would never use, but that's how it would work. 


Here is a good example using a pointer to a pointer to a function: 
```
void printInt(int& a){ 
	cout << a << endl;
}

int main(){ 
	void (*f)(int) = printInt;
	auto* p = &f;
	(*(*p))(42); // prints out 42
}
```
Double dereferencing here in order to call the function. 