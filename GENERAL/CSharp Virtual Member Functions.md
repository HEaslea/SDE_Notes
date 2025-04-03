```
public class Base 
{ 
	public virtual void Display() 
	{ 
		//....
	}
}

public class Derived : Base
{ 
	public override void Display()
	{ 
		//.... 
	}
}
```

In C++, if a derived class declares a method with the same name as a base class method without `virtual`, it hides the base class method rather than overriding it. 
```
clas Base 
{ 
public: 
	virtual void display() { //... }
	void hide() { //... }
};

class Derived : public Base
{ 
public: // overriding 
	void display() override { //... } // This is 
	// this will hide, not override
	void hide() { //... }
};
```
The main thing here is that C++ allows you to hide methods implicitly, we don't have a keyword for it, we just reuse the name. 
C# requires use to use explicit keywords (`override, new`) in order to override of hide (duh), promoting clearer code. There is not much else than that. 

### No Multiple Inheritance
C++ obviously loves some multiple inheritance, however, C# will not stand for it. 

There are `Interfaces` in C#, not sure exactly what they do yet. 


### Abstracts
Remember that we create abstract classes in C++ by declaring at least one pure virtual function; Usually this is where we get out interfaces from, requiring any derived class to implement the pure virtual that we have written. 
```
C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ 
class IInterface 
{ 
public: 
	virtual void method() = 0; // Pure virtual function
	// making this an abstract class
};
C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ C++ 
```

However, C# has its own interface situation going on: 
There is also the distinction between `abstract` and `interface` in C# as well.
```
C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# 
public interface IInterface
{ 
	void Method();
}

public abstract class AbstractClass
{ 
	public abstract void Method();
}
C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# C# 
```
I believe that C# uses vtables as well, or a similar method. 
Here `abstract` achieves the same effect as having pure virtual functions. 

### Example
```
public class Animal 
{ 
	public virtual void Speak() 
	{ 
		Console.WriteLine("Animal Speaks:");
	}
}

public class Dog : Animal 
{ 
	public override void Speak() 
	{ 
		Console.WriteLine("Dog Barks:");
	}
}

public class Cat : Animal 
{ 
	public override void Speak() 
	{ 
		Console.WriteLine("Cat Meows:");
	}
}

Animal animal1 = new Dog();
Animal animal2 = new Cat();

animal1.Speak(); // Dog Barks;
animal2.Speak(); // Cat Meows;
```


Remember that in C++ you can achieve something similar with: 

```
class Base 
{ 
public: 
	virtual void Speak() 
	{
		cout << "Base" << endl;
	}
};

class Derived : public Base
{ 
public: 
	void Speak() override
	{ 
		cout << "Derived" << endl;
	}
};

int main() 
{ 
	Base b; b.Speak(); // Base
	Derived d; d.Speak(); // Derived
	Base& bR = d; 
	bR.speak(); // Derived

	std::unique_ptr<Base> bD = std::make_unique<Derived>();
	bD->Speak(); // Derived
}
```


|Feature|C++|C#|
|---|---|---|

|   |   |   |
|---|---|---|
|**Keyword for Virtual**|`virtual`|`virtual`|

|   |   |   |
|---|---|---|
|**Keyword for Overriding**|`override` (since C++11)|`override` (mandatory)|

|   |   |   |
|---|---|---|
|**Multiple Inheritance**|Supported|Not supported for classes; use interfaces instead|

|   |   |   |
|---|---|---|
|**Pure Virtual Functions**|`virtual void func() = 0;`|`abstract void Func();` in abstract classes|

|   |   |   |
|---|---|---|
|**Preventing Overrides**|`final` (C++11)|`sealed` keyword|

|   |   |   |
|---|---|---|
|**Method Hiding**|Implicit if not virtual, can use `hide` (C++11)|Use `new` keyword|

|   |   |   |
|---|---|---|
|**Access Modifiers**|Any (public, protected, private)|Typically `public` or `protected`|

|   |   |   |
|---|---|---|
|**Destructor Handling**|Requires virtual destructors for proper cleanup|Managed by garbage collector; no destructors|

|   |   |   |
|---|---|---|
|**Virtual Table (vtable)**|Implemented manually by compiler|Implemented by CLR automatically|

|   |   |   |
|---|---|---|
|**Abstract Classes**|Created using pure virtual functions|Explicitly using `abstract` keyword|

|   |   |   |
|---|---|---|
|**Interface Support**|Simulated via abstract classes|First-class language construct (`interface`)|

|   |   |   |
|---|---|---|
|**Performance Optimizations**|Manual optimizations possible (e.g., devirtualization)|JIT compiler optimizations handled by CLR|
Remember in C++, references are essentially just addresses. 

