Inheriting allows to acquire properties (data members) and behaviours (member functions) from another class (base class). 
```
class Base
{ 
public:
	int baseData;
	virtual void baseFunction();
};

class Derived : public Base
{ 
public:
	int derivedData;
	void derivedFunction();
};
```

Remember the access modifiers `public` `protected` `private`, which changes how things are inherited into the derived class. 

```
class Base
{ 
public: 
	int a;  // 4 bytes
	double b; // 8 bytes
};

class Derived : public Base
{ 
public: 
	char c; // 1 byte
	// padding for alignment if needed
};
```

> **Memory Layout**
> Derived object: 
> 	Base subobject
> 		int a : 4 bytes
> 		double b : 8 bytes
> 	char c : 1 byte

Before padding, we have 13 bytes, then after padding, we can adjust to the nearest multiple of 4. To 16 bytes. 

The Derived object contains the Base subobject first, followed by its own members. 

```
class Base
{ 
public: 
	virtual void baseFunction();
	int a;
};

class Derived : public Base
{ 
public: 
	void baseFunction() override; 
	int b;
};
```
> **Memory Layout**
> Base Object: 
> 	vptr: pointer to the virtual table (typically 8 bytes on 64-bit systems)
> 	int a : 4 bytes
> 	Total 12 bytes

> Derived object: 
> 	Base Subobject: 
> 		vptr : 8 bytes
> 		int a : 4 bytes
> 		Padding 4 bytes
> 	int b : 4 bytes
> 	Total 16 bytes

**vptr** : Virtual Pointer : each object with virtual functions contains a hidden pointer to a virtual table (vtable) that holds addresses of virtual functions. 
Here the `Derived` class reuses the `vptr` from `Base` but its vtable points to `Derived::baseFunction()`. 

```
class Base1 {
public:
    int a;
    virtual void func1();
};

class Base2 {
public:
    double b;
    virtual void func2();
};

class Derived : public Base1, public Base2 {
public:
    char c;
    void func1() override;
    void func2() override;
};
```

**Memory Layout:**

- **Derived Object**:
    - **Base1 subobject**:
        - `vptr1`: Points to `Base1`'s vtable
        - `int a`: 4 bytes
    - **Base2 subobject**:
        - `vptr2`: Points to `Base2`'s vtable
        - `double b`: 8 bytes
    - `char c`: 1 byte
    - **Total**: Depending on padding, likely 24 bytes


#### Functions Within Inheritance
###### Function Overriding
```
class Animal 
{ 
public: 
	virtual void speak()
	{ 
		cout << "Animal Speaks" << endl;
	}
};

class Dog : public Animal
{ 
public: 
	void speak() override
	{ 
		cout << "Dog Barks" << endl;
	}
};
```
```
Animal* animal = new Dog();
animal->speak(); // outputs Dog Barks
```

##### Function Hiding
When the base class function is not virtual, then we are going to hide that function from the base, in the derived. 
Here the base class function will be hidden in the derived classes scope. 

```
class Base
{ 
public: 
	void display(int x)
	{ 
		cout << "Base Display : " << x << endl;
	}
};

class Derived : public Base
{ 
public: 
	void display(double y) 
	{ 
		cout << "Derived Display: " << y << endl;
	}
};

Derived d;
d.display(5.5); //Calls Derived::display(double)
// d.display(5); // ERROR as Base::display(int) is hidden in derived

// this one works
d.Base::display(5); // outputs Base Display: 5
```

#### Virtual Functions / Dynamic Dispatch
The function run, will be determined at run-time, (slowing us down), however, we sort of ignore the type of the pointer and the reference, rather look at the actual type that the pointer points to. 
This is the way to support polymorphic tendencies in C++. 
```
class Shape 
{ 
public: 
	virtual void draw()
	{ 
		cout << "Drawing Shape " << endl;	
	}
};

class Circle : public Shape
{ 
public: 
	void draw() override
	{ 
		cout << "Drawing Circle" << endl;
	}
};

Shape* nC = new Circle();
nC->draw(); // Ouputs Drawing Circle
```
The same function call, on what appears to be the same object type, can have different performances. 
**vptr** each object of a class with virtual functions contains a hidden pointer to a virtual table (vtable). 
**vtable** A table of function pointers for the virtual functions accessible by the object. 
When we call a virtual function, the program uses the vptr to access the appropriate function it the vtable. 

#### Constructors and Destructors in Inheritance
Base Class Constructors ARE NOT INHERITED by derived classes. 
Must be Explicitly called by the derived class constructors, either implicitly or explicitly. 

Derived class Constructors can initialize base class members through ctor initializer list. 

```
class Base
{ 
public:
	Base(int x) { cout << "Base Ctor" << endl;}
};

class Derived : public Base
{
public: 
	Derived(int x, int y) : Base(x) { cout << "Derived Ctor" << endl; }
};
```

#### Using Using
```
class Base
{ 
public: 
	void F() { cout << "F BASE" << endl;}
	void g() { cout << "G BASE" << endl;}
	void h() { cout << "H BASE" << endl;}
};

class Dervied : private Base // notice private inheritance
{ 
public: // gonna move f; here 
	using Base::f;
	void d() { cout << "D DERIVED" << endl;}
	void n() { h(); }
};

int main() 
{ 
	Derived d;
	d.f(); // outputs F BASE
	d.d(); // outputs D DERIVED
	d.n(); // outputs H BASE
}
```
