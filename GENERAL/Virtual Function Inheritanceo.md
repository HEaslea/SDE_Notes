When we inherit, al the public and protected virtual functions are inherited, by the derived class. 
```
class Base
{ 
public: 
	virtual void show() 
	{ 
		std::cotu << "Base::show()" << std::endl;
	}
};

class Dervied : public Base
{ 
	// inheriting Base::show();
};

int main() 
{ 
	Derived d; 
	Base* bPtr = &d;
	bPtr->show(); // calls Base::show();
}
```
Then overriding them uses dynamic dispatch as per usual: 
```
// Base as before
class Derived : public Base
{ 
public: 
	void show() override 
	{ 
		std::cout << "Derived::show()" << std::endl;
	}
};

int main() 
{ 
	Derived d
	Base* bPtr = &d;
	bPtr->show(); // Derived::show() called here;
}
```

They seem to work like normal functions in that sense, until they are overridden, then the normal dynamic dispatch will occur. 

