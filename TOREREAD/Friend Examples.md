```
class GFG{
private: 
	int private_int; 

protected: 
	int protected_int;

public: 
	GFG()
	{ 
		private_int = 10; 
		protected_int = 99;
	}

	// friend class here means that F can access the private and the protected values of GFG
	friend class F; // crucial
};


class F{ 

public: 
	void display(GFG& t){ 
		cout << t.private_int << endl;
		cout << t.protected_int << endl;
	}
};


int main(){ 
	GFG g; // intialized ints
	F fri; 
	fri.display(g); // without friend this would run a compile error in the display member function
	
	return 0;
}
```
Think of it like, my friend is allowed into my house, so if a class were to make another a friend, then the **other** class can come in and use it's private and protected. 

```
class Base{ 
private: 
	int privint; 

protected: 
	int protint;

public: 
	Base()
	{ 
		privint = 10; protint = 99;
	}

	friend void friendFunction(base& obj);
};

void friendFunction(base& obj)
{ 
	// whatever you wanna do here that could access the privint and protint
}
```


```
class base; // forward declaration needed as it will be used in this next class

class anotherClass { 
public: 
	void memberFunction(base& obj);
};

class base{ 
private: 
	int privint; 
protected: 
	int protint;
public: 
	base() 
	{
		privint = 10;
		protint = 99;
	}

	friend void anotherClass::memberFunction(base&);
};

void anotherClass:memberFunction(base& obj)
{ 
	// whatever you want this class to do
}
```


A friend function is able to access members without the need of inheriting the class. 

Acts as a bridge between two classes by accessing their private data. 

Increase versatility of overloaded operators. 

Declared either in the public/private or protected part of the class. 



Friend functions have access to private members of a class from outside the class which violates the law of data hiding. 

Cannot do run-time polymorphism in their members. 

Here is one of my own design that I was struggling to think about a bit: 

```
class FriendTest{ 
public: 
	FriendTest()
	{ 
		ObjIntValue = 20;
		Name = "NewName"; // default constructor, initialize
	}
	friend int ReturnClassPrivateMember(FriendTest& obj);
	string ReturnName();
	void SetName(string&);

private: 
	int ObjIntValue;
	std::string Name;
};	

int ReturnClassPrivateMember(FriendTest& obj){ 
	return obj.ObjIntValue;
}

void FriendTest::SetName(sring &s){ // this means that the string we pass in has to be initialized, can't pass a string literal
	Name = s;
}

string FriendTest::ReturnName(){ 
	return Name;
}

int main(){ 
	FriendTest newobj; 
	string name("Howard");
	newobj.SetName(name);
	cout << newobj.ReturnName() << endl;
	cout << ReturnClassPrivateMember(newobj) << endl;
}

