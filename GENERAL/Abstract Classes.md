They serve as a blueprint for other classes, defining common methods, and properties that subclasses must implement, as they are not defined in this class itself. 

They usually contain one or more **pure virtual functions**, which is a function that has no implementation. 

```
class AClass { // for abstract class
public: 
	// pure virtual function declaration 
	virtual void pureVirtualFunction() = 0;

	// Normal Member Functions
	void concreteFunction() { 
		// Implementation, just an example, not in AClasses 
	}
};
```


The characteristics here are: 
- Cannot be instantiated, they have pure virtual functions. Creating an instance of an abstract class will create a compiler error
- Although they might not have any objects, they may have functions. 

```
class Entity
{
public:
    virtual std::string GetName() = 0;
};

class Player : public Entity
{
private:
    std::string m_Name;

public:
    Player(const std::string& name)
        : m_Name(name) {}
};

int main(){  
    Player Hugo;
```

This would result in a compile error, as we haven't defined `GetName` in the inheriting classes. 
However if we add: 
```
std::string GetName() override { return m_Name; }
```

Then we are fine to instantiate an object any way we want. 

If we wanted to do this: 
`Entity* e = new Entity();` we have an issue, as objects of abstract classes are not allowed. 

Doing something like this is fine, even though we have `Entity*` as our parameter in that Function; remember that is fine and we can indeed do that lol, this is because of the "x is a" type of saying, meaning that `Player` is an `Entity`, therefore it can call functions that would parameterise an `Entity`. 

```
Player *p = new Player("Hugo"); // remember that no default constructor for no argument initialization
cout << p->GetName() << endl;

delete p;
```

This will just output `"Hugo"` and that is really cool. 

Now this idea of inheriting classes, having to define the function that the base class has declared, as a pure virtual function, means that we can pass objects of the base/abstract class into functions, KNOWING FULL WELL that the subclasses (inheriting classes) have that function within it to call. 
IT MEANS THAT WE ARE GUARANTEED THAT KNOWLEDGE AND IDEA, THE INTERFACE IS GUARANTEED.

Now imagine that I want to call a function that will print the class of an object. 
So here, what we might do is create a base class/abstract class at the top of the hierarchy. 
It will have a pure virtual function that will return a string that is the class type. 

```
class Printable
{
public:
    virtual std::string GetClassName() = 0;
};

class Entity : public Printable // ACLASS
{
public:
    virtual std::string GetName() = 0;
    std::string GetClassName() override { return "Entity";}
};

class Player : public Entity // ICLASS // direct inheritance from Printable
{
private:
    std::string m_Name;

public:
    Player(const std::string& name)
        : m_Name(name) {}

    std::string GetName() override { return m_Name; }
    std::string GetClassName() override { return "Player";}
};

  
void PrintName(Entity* entity) // Not base class Printable
{
    std::cout << entity->GetName() << std::endl;
}

void PrintClass(Printable* printobj)
{
    std::cout << printobj->GetClassName() << std::endl;
}

  

int main(){  
    // Entity* e = new Entity();
    // Printable* P = new Printable();

    Player* p = new Player("Hugo");
    cout << p->GetName() << endl;
    PrintClass(p);

    delete p;
```


Just have a read and see how the Printable Class , really just makes sure that the subclasses have a function that will print out the type of the class. 

You might want to clear ambiguity  with the following cope accessor: 
```
D obj; 
obj.B::Foo();
obj.C::Foo();
```



Another Example: 
```
class Command
{ 
public: 
	virtual ~Command() {} // virtual destructor

	virtual void execute() = 0;
};

class ConcreteCommand : public Command
{ 
public: 
	~ConcreteCommand() {  // cleanup specific to ConcreteCommand }

	void execute() override { // Implementation of Execute }
};

int main(){ 
	Command* cmd = new ConcreteCommand(); 
	// use cmd

	delete cmd; // Proper cleanup due to virtual destructor
}
```

We have to do the whole `virtual ~Command() {}` here as a destructor, so that we define specific polymorphic destructors; because, we can call a function using a pointer to Command, even though the object we are functioning on is a subclass, so when we delete, we might delete a derived class object from a base class pointer, meaning that the base class destructor will be called. This can be very problematic, so when we do this, we want to make sure that the right destructor is called. 

### Late and Early Binding
#virtual 
#latebinding
#earlybinding
One of the main reasons that we use virtual is the idea of late and early bindings.

When we have the idea that we can use a base pointer to a derived class; when we call a member function of that object, we might call it on the type of the pointer, or we might call it on the type that the base pointer points to. 
This will elude to why this is so powerful.

eg. 
```
Base* newobject = new Derived(); 
```

Now, if Base, the class itself, had a bunch of functions that didn't use the virtual command, then we might struggle to call the derived class's functions. 

eg. This is done **NOT USING** virtual functions, this will cause early bindings. 
```
class Shape{ 
public: 
	static string ST;
	int radius = 5;
	void Drawing(){ 
		cout << "Drawing " << ST << endl;
	}
};
string Shape::ST = "BASE SHAPE";

class Circle : public Shape{ 
public: 
	static string ST;
	int radius = 10;
	void Drawing(){ 
		cout << "Drawing " << ST << endl;
	}
};
string Circle::ST = "CIRCLE";

int main(){ 
	Shape* nCircle = new Circle; // Base pointer, but nCircle is a circle
	nCircle->Drawing();
	cout << nCircle->radius << endl;
}
```
 Then output will be: 
 ![[Pasted image 20240326032001.png]]
 This is because of early binding, there is no `virtual` keyword, that would lead these base level functions to be overridden, when the calling object is not specifically of base class type. 

  However, if we put the `virtual` word in the base class before the function, and the `override` keyword in the derived class. 

```
class Shape{
public:
    static string shapetype;
    int radius = 5;
    virtual void Drawing(){
        cout << "Drawing " << shapetype << endl;
    }
};
string Shape::shapetype = "BASE SHAPE";

class Circle : public Shape{
public:
    static string shapetype;
    int radius = 10;

    void Drawing() override {
        cout << "Drawing " << shapetype << endl;
    }
};
string Circle::shapetype = "CIRCLE";

int main(){
    Shape* nCircle = new Circle;
    nCircle->Drawing();
    cout << nCircle->radius << endl;
}
```
![[Pasted image 20240326032337.png]]
Note here that the 5 is still the same. 
This is why we do the getter and the setter thing, as there are no early bindings and late bindings when it comes to data members. 

This is why we would do something like
```
// in Shape
	virtual int GetRadius(){ 
		return radius;
	}

// in Circle
	int GetRadius() override{ 
		return radius;
	}

// in int main()
cout << nCircle->GetRadius() << endl;
```
This is pretty strong,  as we can use the same data member name, using base pointers, without running into this error, hence why it is to this way at the beginning and doesn't really seem like it is a big deal, but it is. 