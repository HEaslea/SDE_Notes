```
#include <iostream>

class MyClass {
public:
    // Member function with lvalue reference qualifier
    void myFunctionLvalue() & {
        std::cout << "Called on lvalue\n";
    }

    // Member function with rvalue reference qualifier
    void myFunctionRvalue() && {
        std::cout << "Called on rvalue\n";
    }
};

int main() {
    MyClass obj1;
    obj1.myFunctionLvalue(); // Calls myFunctionLvalue()

    MyClass().myFunctionRvalue(); // Calls myFunctionRvalue()

    // Error: Cannot call myFunctionRvalue() on lvalue
    // obj1.myFunctionRvalue(); 

    // Error: Cannot call myFunctionLvalue() on rvalue
    // MyClass().myFunctionLvalue();

    return 0;
}
```

My Code: 

```
class Simple{ // turned out not so simple
private: 
	int value;
public: 
	// usually shiz here
	// just going to add this here to indicate that using 
	// operator<< for our object for the two members that 
	// we have below
	
	friend std::ostream& operator<<(std::ostream& os,
		const MyClass& obj) { 
		os << "  Printing Simple obj " << obj.value;	
	}

	// reference qualifiers here
	void LValueFunct() & { 
		cout << "Called on lvalue : " << *this << endl;
	}

	void RValueFunct() && { 
		cout << "Called on rvalue : " << *this << endl;
	}
	// these are forcing us to pass in an lvalue or an rvalue
	// there are no other types here
};
```
```
int main(){ 
	Simple a(10); // creating an lvalue here
	a.LValueFunct();  // passing an lvalue
	std::move(a).RValueFunct();// passing an rvalue
}
```

Ignore the copy constructor bs here: 
![[Pasted image 20240421220553.png]]

Remember that rvalues are temporary objects. 

I could do something like this: 
```
Simple().RValueFunct();
// This will output value as the default value we set it to
// in our constructor, or our initializer
// which in this case is 0
```

**We wouldn't be able to do something like this**: 
```
a.RvalueFunct(); // as a is an lvalue
```

**Remember that when we return by value, the value aspect should tell us that we are returning by rvalue**. 

```
class Employee{ 
private:
	std::string m_name{};

public: 
	Employee(std::string_view name): m_name{ name } {}
	const std::string& getName() const { return m_name; }
	// getter returns by const reference
};

Employee createEmployee(std::string_view name)
{ 
	Employee e{ name };
	return e;
} // as return is by value, then it casn be treated as an rvalue

int main()
{ 
	// Case 1: okay; use returned reference to member of rvalue
	// of class object in same expression
	std::cout << createEmployee("Frank").getName() << "\n";

	// Case 2: bad: save returned reference to member of rvalue
	// class object for use later
	const std::string& ref { createEmployee("Garbo").getName()};
	// reference becomes dangling when return value of createEmployee() is destroyed 
	std::cout << ref << "\n"; // undefined behaviour
}
```

So the object returned by `createEmployee("Garbo")` is destroyed after initializing `ref`. 


Returning by value: 
- returning a copy of the object, that has a lifetime outside of the object that copied it. 
- This means that any modifications to the object that is returned, will not change the values of the original object. 
- The caller is then the owner of the value that is returned, they are responsible for managing its lifetime, eg. deleting allocated memory if necessary.
- We return a copy of the object entirely.

Returning by reference: 
- No copying at all, you are returning a reference to the original object, so it's like you returning the actual object. 
- The caller has direct access to that object. 
- If the original object goes out of scope, then we the reference will be dangling
- more efficient, no copying involved, depends if you want the original or not. 

I think to some degree, you have to abstract thinking of the bytes and bits, in order to think about how the language will work, if we say that the whole object is returned, don't think too much about the bytes and how they work here. 

[[Function Qualifiers]]