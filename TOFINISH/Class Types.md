#classes
#definingabstractdatatypes [[Defining Abstract Data Types]]



Every class defines a unique type. 


Two classes will define two different types, even if they are the same member. 

eg: 
```
struct First { 
	int memi; 
	int getMem(); 
};

struct Second { 
	int memi; 
	int getMem();
};

First obj1; 
Second obj2 = obj1; // Obvious Error
```

We can also put `class` before: 
`Sales_data item1; class Sales_data item1;` both are the same here. 
The second being more from C. 

### Class Declarations
`class Screen;` would be a declaration. 
This is sometimes referred to as a forward declaration, introducing the name `Screen` into the system. This would be an incomplete type before any definition. 

Obviously there is limited use for such a thing. 
We can define pointers and references to such a type, we can declare, without defining functions too, where the type is used as a parameter or a return type. 

Yet a class must be defined - not just declared. Otherwise the compiler will not know how much storage to use for such a thing. 
A class cannot have a data member of its own type. 
A class however, due to what was mentioned above, can have references and pointers to a type of itself, which is odd, but it works. 

```
class Link_screen{ 
	Screen window;
	Link_screen *next;
	Link_screen *prev;
};
```

I  think you have the right idea when you think about the code telling the compiler part by part, like if you were reading, we need declarations to know that something exists, then we can come into more detail about it later. 

```
struct X; struct Y; 

struct X{ 
	Y* pointerY;
};

struct Y{ 
	X objectX;
};
```
 The declarations here are crucial, they have to be here, otherwise we cannot place a pointer in the definitions. 

### Friendship Revisited: 
A class can make another class its friend, or it can declare a specific member functions of another class as friends. 
A friend function can be defined inside the class body, they would be implicitly `inline`. 

**Friendship between classes**
EG. our `Window_mgr` class will have members that will need access to the internal data of the `Screen` objects it manages. 

We want to add a member, named `clear` to `Window_mgr` that will reset the contents of a particular screen, to all blanks. 
`clear` will therefore need to access the `private` data members of `Screen`. 
`Screen` can designate `Window_mgr` as its friend: 
```
class Screen{
// Window_mgr members can access the private parts of class Screen
	friend class Window_mgr; 
	// rest of the screen class
};
```

The member functions of a friend class can access all the members, including the non`public` members, of the class granting friendship. 
Now that `window_mgr` is a friend of `Screen`, we can write the `clear` member of `Window_mgr` as follows: 
```
class Window_mgr{ 
public: 
	// location ID for each screen on the window
	using ScreenIndex = std::vector<Screen>::size_type;
	// reset the Screen at the given position to all blanks
	void clear(ScreenIndex);

private:
	std::vector<Screen> screens{Screen(24,80,'')};
};

void Window_mgr::clear(ScreenIndex i)
{ 
	// s is a reference to the Screen we want to clear
	Screen &s = screen[i];
	// reset the contents fo that Screen to all blanks
	s.contents = string(s.height * s.width, '');
};
```
We start by defining `s` as a reference to the `Screen` at position `i` in the `screens vector`. 
Then using the height and width members of that screen, we compute a new string that has the appropriate number of blank characters. 
Then we assign that string of blank characters to the `contents` member. 

If clear were not a friend of `Screen`, it wouldn't compile. 
The `clear` function would not be allowed to use the `height, width` or `contents` of `Screen`. 
`Screen` granting friendship, means that all the members of screen are accessible to the functions in `Window_mgr`.

Remember that `friend` is not transitive. 
If class `Window_mgr` has its own friends, those friends have no special access to `Screen`. 
Like if you had a friend of a friend, it would be weird to just turn up at their house, but your immediate mate's house is sort of fine. 
You gotta be a direct mate to use his coffee cups and sorts. 

#### Making a Member function a Friend
Rather than just making the whole class a friend, we can instead specify that only the clear member is allowed access. 
We will ahve to specify within in the class that we want access to, that the other class's member function is going to access those data members. 

Therefore we would have something that looks like this: 
```
class Screen{ 
	// Window_mgr;; clear must have been declared before class Screen
	friend void Window_mgr::clear(ScreenIndex);
	// rest of Screen class
};
```
This will require careful structuring of our programs to accommodate interdependencies among the declarations and definitions. 
We must order our program as follows now: 
- Define the `Window_mgr` class, which declares, without defining, `clear`. `Screen` must be declared before `clear` can use the members of Screen. 
- Define class `Screen`, including our friend declaration for `clear`.
- Define `clear`, now that we can now refer to the members in `Screen`.

Although overloaded functions do share a name, they are still different functions. 
Therefore, a class must declare as a friend each function in a set of overloaded functions that it wishes to make a friend: 

```
// overloaded storeOn functions
extern std::ostream& storeOn(std::ostream &, Screen&); 
extern BitMap& storeOn(BitMap&, Screen&); 
class Screen{ 
// ostream version of storeOn may access the private parts of Screen objects
	friend std::ostream storeOn(std::ostream &, Screen &);
//...
};
```

Our class `Screen` here takes a version of `storeOn` that takes an `ostream&` its friend. 
There version that takes a `BitMap&` has no special access to `Screen`. 
Only the defined overloaded function is allowed access to the members of the class they are a Friend of. 

#### Friend Declarations and Scope
Classes and nonmember functions need to be declared before they are used in a friend declaration. 

When a name first appears in a friend declaration, that name is implicitly assumed to be part of the surrounding scope. 
However, the friend itself is not declared in that scope. 

Even if we define the function inside the class, we must still provide a declaration outside of the class itself to make the function visible. 

```
struct X{ 
	friend void f() { /* friend function can be defined in the class body */}
	X() { f();} // error: no declaration for f
	void g(); 
	void h();
};

void X::g() {return f();} // error: f has not been declared
void f(); // declares the function defined inside X
void X::h() { return f();} // fine as declaration for f is now in scope
```
A friend declaration affects access but is not a declaration in an ordinary sense. 


Remember that the compilers do no enforce the lookup rules for friends. 

