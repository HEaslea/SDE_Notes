#classes #classesstart 
#definingabstractdatatypes 

Classes will define their own scope. 
Outside that scope, ordinary data and function members may be accessed only through an object, a reference or a pointer using a member access operator. 
We access type members from the class using the scope operator. 
This is like when you use `std::string`

```
Screen::pos ht = 24, wd = 80; // use the pos type defined by Screen
Screen scr(ht, wd, '');
Screen *p = &scr;
char c = scr.get(); // fetches the get member from the object scr
c = p->get(); // fetches the get member from the object to which p points
```

### Scope and Members Defined outside the class
This explains why we must provide the class name as well as the function name when we define a member function outside its class. 

Outside the class, the names of the members are hidden. 

Once the class name is seen, the remainder of the definition - including the parameter list and the function body - is in the scope of the class. 

Therefore we can refer to other class members without qualification, it is like we walk into the classroom and we can just refer to stuff in there, without having to refer to the classroom itself. 

Remember the `clear` member in `Window_mgr`. That function's parameter uses a type that is defined by `Window_mgr`: 
```
void Window_mgr::clear(screenIndex i){ 
	Screen &s = screens[i];
	s.contents = string(s.height*s.width, '');
}
```
Because the compiler sees the parameter list after nothing that we are in the scope of class `Window_mgr`, there is no need to specify that we want the `ScreenIndex` that is defined by `Window_mgr` ( we are there, we are in the `Window_mgr` building, we don't need to reiterate that any further). 
For the same reason, the use of `Screens` in the function body refers name declared inside class `Window_mgr`. 
however, the return type of a function normally appears before the function's name. 
When a member function is defined outside the class body, any name used in the return type is outside the class Scope too!

The return type therefore, must also specify the class of which it is a member. 
If we gave `Window_mgr` a function, named `addScreen`, to add another screen to the display. 
This member will return a `ScreenIndex` value that the user can subsequently use to locate this `Screen`: 
```
class Window_mgr { 
public: 
	// add a screen to the window and returns its index
	Screenindex addScreen(const Screen&); 
	// other members as before
};

// return type is seen before we're in the scope of Window_mgr so we gotta walk there beforehand
Window_mgr::ScreenIndex Window_mgr::addScreen(const Screen &s)
{ 
	screens.push_back(s);
	return screen.size() - 1;
}
```

Because the return type appears before the name of the class is seen , it appears outside the scope of class `Window_mgr`. 

To use `ScreenIndex` as the return type, we must specify in which class the type is defined. 

### Name Lookup and Class Scope
#namelookup
Name lookup so far has been really simple: 
- First, look for declaration of the name in the block in which the name was used. Only names declared use are considered. 
- If the name isn't found, look in the enclosing scope(s).
- If no declaration found, then the program is in error. 

The way names are resolved inside member functions defined inside the class may seem to behave differently than those lookup rules, however, yet they don't lol. 

Class definitions are processed in two phases: 
- First, member declarations are compiled. 
- Function bodies are compiled only after the entire class has been seen. 

Member function definitions are processed **after** the compiler processes all of the declarations in the class. 

We would have to order functions in a way that means that they use functions that the compiler sees, if a function uses another that ahs not been seen yet, then we will struggle, that is why we declare. 

##### Name Declarations for Class Member Declarations
Names used in declarations, including names used for the return types and types in the parameter list, must be seen before they are used. 

If a member declaration uses a name that has not yet been seen inside the class, the compiler will look for that name in the scope(s) in which the class is defined. 
eg: 
```
typedef double Money;
string bal;
class Account{ 
public:
	Money balance() { return bal; }
private: 
	Money bal;
	// ...
};
```

When the compiler sees the declaration `balance` function, it will look for a declaration of `Money` in the `Account` class. 

So the compiler will look for declarations of stuff, inside the class first, before looking at the enclosing scope. 

Here it will find the `typedef` of `Money`. 
On the other hand, the body of that function is processed only after the entire class is seen. 

Therefore, the `return` inside the that function returns the member named `bal`, not the `string` from the outer scope. 
So declarations inside classes will work inside then out, makes way more sense of course. 

### Type Names are Special

An inner scope can redefine a name from an outer scope even if that name has already been used in the inner scope. 

In a class though,  if a member uses a name from an outer scope, and that name is a type, then the class may not subsequently redefine that name: 
```
typedef double Money; 
class Account { 
public: 
	Money balance() { return bal;} // Uses Money from the outer scope

private: 
	typedef double Money; // cannot redefine money, it was already set in compiler from the earlier / outer scope
	Money bal;
	//... 
};
```
Even though they use the same type, this will still cause an error. 

Compilers will not always diagnose this error. Some will just accept the error lol. They might just quietly get on with it, and you will run into a run-time bug. 

Definitions of type names usually **should** appear at the beginning of a class. 
That way any member that uses that type will be seen after the type name has already been defined. 


#### Normal Block-Scope Name Lookup inside Member Definitions

A name used in the body of a member function is resolved as so: 
- First, look for declaration of the name inside the member function, only decs in the body that precede the use of the name are considered duh. 
- Then, not there, look for a declaration inside the class. All members of the class are considered. 
- If a dec is not in the class, enclosing scope that would be before the class/member function. 

Normally, a bad idea to use the same name of another member as the name for a parameter in a member function, this is common sense
- First, look for declaration of the name inside the emmbermember function, Only decs tonly decs in the body body that precede the use of the name are considered duh.  
- Then, not there, look for a declaration inside the class. All members are of the class are considered.  
- If a dec is not in the class, enclosing scope that would be before the class/member function.  

Normally, a bad idea to use the same name of another member as the name for a parameter in a member function, this is common sense. 

Common good practices will 

For more clarity here: 
```
// NOTE this code is for illustration, as it reflects bad practices
// bad idea to use the same name for a parameter and a member
int height; // defines a name subsequently used inside Screen

class Screen{ 
public: 
	typedef std::string::size_type pos;
	void dummy_fcn(pos height){ 
		cursor = width * height; // which height? the parameter duh
	}

private:
	pos cursor = 0; 
	pos height = 0, width = 0;
};
```
In this case, the parameter  hides the height that is found outside the class. 
Obviously the better thing to do is rename the parameter to a different thing. 

```
// good practice: don't use a member name for a parameter or other local variable. 
	void Screen::dummy_fcn(pos ht){ 
		cursor = width * height; // member height and not the paramete ht
	}
```

Even though `height` appears after its use in `dummy_fcn` , the compiler resolves this use to the data member named `height`. 

#### After class Scope, Look in the Surrounding Scope
If not found name in the function or class scope, will look for in the surrounding scope. 
Remember here the `::height` would refer to the global scope. 

like here: 
```
// bad practice: don't hide names that are needed from surrounding scopes duh
	void Screen::dummy_fcn(pos height){ 
		cursor = width * ::height; // which height? the global one
	}
```

### Names are Resolved where they appear within a File
When a member is defined outside its class, the third step of name lookup includes names declared in the scope of the member definition as well as those that appear in the scope of the class definition. 
```
int height; // defines a name that will be used  in Screen
class Screen{ 
public: 
	typedef std::string::size_type pos;
	void setHeight(pos);
	pos height = 0; // hides the declaration of height in the outer scope
};

Screen::pos verify(Screen::pos);

void Screen::setHeight(pos var){ 
	// var: refers to the parameter here
	// height referes to the class member
	// verify: refers to the global function
	height = verify(var);
}
```

The declaration of the global function `verify` is not visible before the definition of the class `Screen`. 

`verify` can be used in the `setHeight` as it was declared before `setHeight`.

