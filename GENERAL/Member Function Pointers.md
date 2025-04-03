```
class MyClass
{ 
public: 
	void display() 
	{ 
		cout << "Display from MyClass" << endl;
	}
};

int main() 
{ 
	void (MyClass::*memFuncPtr)() = &MyClass::display;

	MyClass obj;

	(obj.*memFuncPtr)(); // Output display from MyClass

	MyClass * objPtr = &obj;

	(objPtr->*memFuncPtr)(); // output display from MyClass
}
```
