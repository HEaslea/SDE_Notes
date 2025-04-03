#upcasting
Used in classes, the general idea is that you can have a pointer to a subclass object, and that type of pointer will be of the base class. 

#commandpattern
[[Command Pattern]]

```
class Base{ 
public: 
	virtual void print(){ 
		std::cout << "Base class print function" << std::endl;
	}
};

class Sub : public Base { 
public: 
	void print() override{ 
		std::cout << "Subclass Print Function" << std::endl;
	}
};

int main(){ 
	Sub subclassObj;

	Base* basePtr = &subclassObj; // Upcasting pointer to sub object assigned
	// to pointer of base class 

	// Calls the subclass's print function even though the pointer is of type Base*
	basePtr->print();
}
```

One way of thinking about it, is like this: 
```
using namespace std;

class Shape{
public:
    virtual double Area() const = 0;
    virtual std::string ShapeType() const = 0;
};

class Circle : public Shape{
    double radius;
	
public:

    Circle(double r) : radius(r) {}

    double Area() const override{
        return M_PI * radius * radius;
    }

    std::string ShapeType() const override{
        return (string)"Circle";
    }
};

class Rectangle : public Shape{
double length, width;

public:

    Rectangle(double l, double w) : length(l), width(w) {}

    double Area() const override{
        return length * width;
    }

    std::string ShapeType() const override{
        return (string)"Rectangle";
    }
};

class Triangle : public Shape{
double base, height;

public:
    Triangle(double b, double h) : base(b), height(h) {}

    double Area() const override{
        return 0.5 * base * height;
    }

    std::string ShapeType() const override{
        return (string)"Triangle";
    }
};

template<typename T>
void PrintVect(std::vector<T>& vec){
    for(auto i : vec){
        std::cout << i << " : ";
    }
    std::cout << std::endl;
}
  

int main(){

    Shape* shapes[] = {new Circle(5), new Rectangle(4, 6), new Triangle(3,8)};

    for(Shape* shape : shapes){
        std:cout << "Area of this shape: " << shape->ShapeType() << "\nArea:" << shape->Area() << "\n\n";
    }
    std::cout << std::endl;
}
```

I think I understand this quite well. 


Here is a really good example, using command patterns, meaning that we can reassign buttons using a class `Command`.

```
class Command{ 
public: 
	virtual ~Command() {} // meaning that the subclass will be used for destructor even if the subclass is of type Command*
	virtual void execute() = 0; // pure virtualf unction, meaning that we have to override it in each subclass
};

class JumpCommand : public Command{ 
public: 
	virtual void execute() { jump(); }
};

class FireCommand: public Command{ 
public: 
	virtual void execute() { fireGun(); }
};

// etc. etc.

// Then in inputHandler, where we call depending on what we have assigned

class InputHandler{ 
public: 
	void handleInput(); 
	// Methods to bind commands

private: 
	Command* buttonX_;
	Command* buttonY_;
	Command* buttonA_;
	Command* buttonB_;
};
// this is the idea of upcasting, meaning that our buttons are pointing to a subclass type, however, we can see that they are actually Command*, this is why we use the virtual destructor; so that we casn destruct the subclasses while using Command*

// finish that function
void InputHandler::handleInput()
{ 
	if(isPressed(BUTTON_X)) buttonX->execute();
	else if(isPressed(BUTTON_Y)) buttonY->execute();
	else if(isPressed(BUTTON_A)) buttonA->execute();
	else if(isPressed(BUTTON_B)) buttonB->execute();
}
// there's a layer of indirection now
// Here we can assign commands

```


Here's how i did it; looks a little different due to circumstances and mimicking inputs: 

```
using namespace std;
void Jump(); 
void shootGun();
void isPressed();

class Command{ 
public: 
	virtual ~Command();
	virtual void execute() = 0;
};

class JumpCommand : public Command{ 
public: 
	virtual void execute() {Jump();}
};

class FireGun : public Command{ 
public: 
	virtual void execute() {shootGun();}
};

class InputHandler{ 
public: 
	void handleInput(); 
	// methods to bind commands, just basic here
	void SetA() { buttonA_ = new JumpCommand;} // would be interchangeable
	void SetB() { buttonB_ = new FireGun;} // would be interchageable

	// NOT new FireGun();
	
private: 
	Command* buttonA_;
	Command* buttonB_;
};

void InputHanlder::handleInput(){ 
	cout << "\n--Input Char: a or b--" << endl;
	char n = isPressed(); // get input
	if(n == 'a') { cout << "a pressed" << endl; buttonA_->execute();}
	if(n == 'b') { cout << "b pressed" << endl; buttonB_->execute();}
	else cout << "\nNOT VALID\n" << endl;
}

int main(){ 
	InputHandler newHandler; 
	newHandler.setA(); newHandler.SetB();

	for(int i = 0; i <= 5; ++i){ 
		cout << "\n NewLoop: " << i << endl;
	}

	return 0
}

char isPressed(){ 
	char c; 
	cin >> c; 
	return c;
}

void ShootGun(){ 
	cout << "\nFIRED GUN\n" << endl; // mimicking firing gun
}

void Jump(){ 
	cout << "\nJUMPED\n" << endl; // mimicking idea of jumping
}

```


So the way I read this is thus: 

When we create an `InputHandler` (`newhandler`), we are creating in instance of buttonA and buttonB, which just point to commands, in the same way that a button is an indirect way of doing an action, in that the button is just connected to a function/object. 
In this case the commands, the subclasses are just objects with a single function (they seem like just a function in that case lol). 
So when we say that `buttonA_` is `Command*`, we mean that `buttonA_` is a pointer to any of the subclasses. 
This means that the button has just an `execute()` function, if we added more to the subclasses, then we can have more to do. 
The nice thing about having an abstract `inputHandler` is that we can do everything with input here, if it is to do with input, just ask the instance of the input handler here. 
The benefit of having a class here is that we can have a lot of inputhandlers that can be initialized a certain way, and the the player can have multiple, or the same obj's can have various ways of changing which button is pointing to which command. 

```
void SetAFire(){ buttonA = new FireGun;}
```

The general idea that I'm taking away as well, is that the subclasses are just functions, meaning that we have to create an instance of that class, to call the function. 









Something to remember with the heap, is that we are not just going to copy a value in there, therefore we use Pointers, so that we can refer to the same object. 
meaning that ultimately you don't need to pass by value, you can just pass the pointer in.

The main thing to remember is that IT IS A POINTER, therefore we would have to use something like `myObject->doSomethingRad();` in order to call that function.

Here is another general idea: 
```
typedef void (*exePtr)();

using namespace std;

void Jump();
void Fire();
void Sprint();
void Ability();

class InputHandler{
    vector<exePtr> buttons; // dynamically sized button range

public: // defining in class cuz why not
    void setButton(exePtr fptr){
        buttons.push_back(fptr);
    }

    void pressButton(int ix){
        if(ix >= 0 && ix < buttons.size()){
            buttons[ix](); // call function button is pointing to
        }
    }
  
    void PressAll() {
        for(exePtr but : buttons){
            but(); // calling the function pointing to
        }
    }
};
  

int main(){
    InputHandler* newHandler = new InputHandler;
    newHandler->setButton(&Jump);
    newHandler->setButton(&Fire);
    newHandler->setButton(&Sprint);
    newHandler->setButton(&Ability);

    newHandler->PressAll();

}

void Jump(){ cout << "\nJUMP" << endl;}

void Fire(){cout << "\nFIRE" << endl;}

void Sprint(){ cout << "\nSPRINT" << endl;}

void Ability(){ cout << "\nABILITY" << endl;}
```


This is absolutely the same sort of deal here; 

ERROR: encountered an error here where our destructor is declared but no defined; this is because we have written `virtual ~Command()` and not `virtual ~Command() {}` the `{}` will mean that it is defined as default, and not just declared, without a definition, meaning that our compiler cannot create one for us. 


With the idea of a vector, what you could do is use an ENUM to map `A, B, X, Y` to ints, which we can use for indexing.
You could also use the idea that `buttons` are their own class, however, we might as well have buttons inside the `InputHandler`, otherwise we will have four button objects, each with just a `Command*` or equivalent

```
using namespace std;

enum eButtons{
    A, B, X, Y
};

void Jump(); void Fire(); void Ability(); void Sprint();

class Command{
public:
    virtual ~Command() {}; // adding {} means it is defined
    virtual void execute() = 0;
};

class JumpCommand : public Command{
public:
    virtual void execute() { Jump(); } //  JUMP
};

class FireCommand : public Command{
public:
    virtual void execute() { Fire(); } // FIRE
};

class AbilityCommand: public Command{
public:
    virtual void execute() { Ability(); } // ABILITY
};

class SprintCommand : public Command{
public:
    virtual void execute() { Sprint(); } // SPRINT
};
  
class InputHandler{
    vector<Command*> buttons;
    // see Enum

public:
    InputHandler() { // default overlay controls
        cout << "\n--Default controls--" << endl;
        buttons.push_back(new JumpCommand);
        buttons.push_back(new FireCommand);
        buttons.push_back(new AbilityCommand);
        buttons.push_back(new SprintCommand);
    }

    void PushAll(){
        for(auto button : buttons){
            button->execute();
        }
    }

    void PushButton(eButtons butI){
        buttons[butI]->execute();
    }
};

int main(){  // MAIN
    InputHandler mHandler;
    mHandler.PushAll();
    mHandler.PushButton(A);
    mHandler.PushButton(B);
}

void Jump(){ cout << "\nJUMP" << endl;}

void Fire(){cout << "\nFIRE" << endl;}

void Sprint(){ cout << "\nSPRINT" << endl;}

void Ability(){ cout << "\nABILITY" << endl;}
```