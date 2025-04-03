#additionalclassfeatures
#classes [[Classes Start]] [[Defining Abstract Data Types]]

Exploring some more features that the past `Sales_data` didn't require from us. 

Including type members, in-class initializers for members of class type, `mutable`data members, `inline` member functions, returning `*this` from a member function, more about how we define and use class types, and class friendship. 

### Class Members Revisited
#classmembersrevisited
We'll now define a pair of cooperating classes named `Screen`and `Window_mgr`. 
#### Defining a Type Member

A `Screen` represents a window on a display. 

Each `Screen` has a `string` member that holds the `Screen's` contents., the three `string::size_type` members that represent the position of the cursor, and the height and width of the screen. 
Type names that are defined by the class are subject to the same access controls as any other member and may be either `public` or `private`:

```
class Screen{
public: 
	typedef std::string::size_type pos;
	
private: 
	pos cursor = 0
	pos heigh = 0, width = 0;
	std::string contens; 
};
```

We defined `pos` in the public part of `Screen` because we want users to use that name. 
Users of `Screen` shouldn't know that it uses a `string` to hold its data. 

Therefore, defining `pos` as a public member, we can hide this detail of how `Screen` is implemented. 
We could also use a type alias too, 

```
class Screen{ 
public: 
	using pos = std::string::size_type;	
	// all the other stuff here too
};
```

They obviously have to appear at the beginning of the class, so that we can use the type throughout the class too. 

### Member Functions of `Screen`
Constructor to define the size and contents of the screen, along with members to move the cursor and to get the character at a given location: 

```
class Screen{ 
public: 
	typedef std::string::size_type pos; 
	Screen() = default; // needed because Screen has another constructor
	// cursor initialized to 0 by its in-class initializer
	Screen(pos ht, pos wd, char c): height(ht), width(wd), contents(hd * wd, c){} 
	char get() const // get the character at the cursor
		{return contents[cursor];} // implicitly inline

	inline char get(pos ht, pos wd) const; // explicitly inline

	Scrren &move(pos r, pos c); // declaration, can be made inline later

private: 
	pos cursor = 0; 
	pos height = 0 , width = 0; 
	std::string contents;	
};
```

Member functions of local class are implicitly inline functions: Remember more astutely, that inline is more a suggestion to the compiler, rather than a command. 

We provided a constructor, the compiler will not automatically generate a default constructor for us. 
We use `= default` to ask the compiler to synthesize the default constructor's defintion for us. 

Then our second constructor `Screen(pos ht, pos wd, char c) : height(ht), width(wd), contents(hd * wd, c){}`, uses the in-class initializer for the `cursor` member. 
If we didn't have that in-class initializer, we would have explicitly initialized `cursor` long with the other members. 

### Making members `inline` 
Classes will often have small functions that can benefit from being inlined. 

Member functions defined inside the class are automatically `inline`. 
The constructors and the version of `get` that returns the char denoted by the cursor are `inline` by default. 
We can explicitly declare a member function as `inline` as part of its declaration inside the class body. 

We can specify `inline` on the function definition that appears outside the class body too: 

```
inline Screen &Screen::move(pos r, pos c) 
{ 
	pos row = r * width; // compute the row location
	cursor = row + c; // move the cursor to the column within that row
	return this; // return this object as an lvalue
}
```
The definition of the member function here, was a little confusing at first sight: 
It's more that we have screen twice, however, when you see this, just look at it and know that we are most probably returning an reference of object screen, and that we are defining the class `Screen`'s move function. 

```
char Screen;;get(pos r, pos c) const // declared as an inline in the class
{
	pos row = r * width; // compute row location
	return contents[row + c]; // return character at the given column
} 
```

It is legal to specify `inline` on both the declaration and the definition. 
Specifying `inline` only on the definition outside the class can make the class harder to read. 

### Overloading Member Functions

Member functions can also be overloaded, so long as the functions differ by the number and/or types of parameters. 

The same function-matching will be used in this process as well. 

Our `screen` class has defined two version of `get`. 
One returns the char currently denoted by the cursor; the other returns the char at a given position specified by its row and column.
```
Screen myscreen;
char ch = myscreen.get(); // calls Screen::get()
ch = myscreen.get(0,0); // calls Screen::get(pos, pos)
```
### mutable Data Members
Sometimes, happens that class has a data member that we want to able to modify, even inside a `const` member function, and we would indicate that using the `mutable` keyword in their declaration. 

A `mutable` data member is never `const`, even when it is a member of a `const` object. 

Remember that `mutable` is indefinitely changeable, that might be the best way to remember it. 

Therefore, we will give `Screen` a `mutable` member named `access_ctr`, which we'll use to track how often each Screen member function is called. 

```
class Screen{ 
public: 
	void some_member() const; 
	
private: 
	mutable size_t access_ctr; // may change even in a const object
	// other members as before
};

void Screen::some_member() const
{ 
	++access_ctr; // keep pa count of the calls to any member function
}
```

Despite the idea that `some_member` is a `const` member function, it can change the value of `access_ctr`. 
That member is a mutable member, so any member function, including `const` functions, can change its value. 

Mutable is mainly used for a `const` object, in which a data member we want to be mutable, and changeable. 

The picture I have is of goo, in a steel box, the box is a `const`. 

### Initializers for Data Members of Class Type
In addition to the `screen` class, we will also define a window manager class, representing a collection of `Screen`s on a given display. 
The class will have a vector of `Screen`s in which each element represents a particular `Screen`. 
We would like the class to have a start up, single default -initialized `Screen`. 
The best way to specify this default value is in the in-class initializer. 

```
class Window_mgr { 
private: 
	// screens this Window_mgr is tracking 
	// by default, a Window_mgr has one standard size blank screen 
	std::vector<Screen> screens {Screen(24, 80, '')}; // in class initializer here using {} 
};
```


#### Functions Returning `*this`
```
class Screen { 
public: 
	Screen &set (char);
	Screen &set(pos, pos, char);
	// other members as before
};
inline Screen &Screen::set(char c){
	contents[cursor] = c; // set the new value at the current cursor location
	return *this; // returning this value as an lvalue
}
inline Screen &Scree::set(pos r, pos col, char ch){ 
	contents[r*width + col] = ch; // set specified location to given value
	return *this;
}
```

The set members return a reference to the object on which they are called. 
These would be lvalues, returning the object itself, not a copy of the object. 

`myScreen.move(4,0).set('#');`

They will execute on the same object. 
Here we first move the cursor in the screen and then set that character. 

Equivalent to: 
```
myScreen.move(4,0);
myScreen.set('#');
```

Had we not returned a reference to an object, we would see this difference to be: 
```
// if move returns Screen not Screen&
Screen temp = myScreen.move(4,0); // the return value would be copied
temp.set('#'); // the contents inside myScreen would be unchanged. 
```

Here move has a nonreference type, therefore, the return would be a copy of `*this`, and set would change the temporary copy, not `myScreen`. 

### Returning `*this` from a `const` Member Function
Adding the function `display`, to print the contents of the `Screen`. 
This won't be changing the `Screen` therefore we can use it as a `const` member function. 

This would mean that `this` is a pointer to a `const` and `*this` is a `const` object. 
Hence the return type would have to be `const Screen&`. 
```
Screen myScreen; 
// if display returns a const reference, the call to set is an error;
myScreen.display(cout).set('*');
```
The call to `set` won't compile. 
The `const` version of display returns a reference to a `const` and we cannot call set on a `const` object. 


### Overloading Based on `const`
Of course, if you remember, you can overload a function based onw whether it is `const`. 
The non`const` version will not be viable for `const` objects; 

Only calling `const` functions on `const` object. Remember the idea of function matching, that the non`const` version will match better to the non`const` call. 

Here we will define a `private` member named `do_display` to do the actual work of printing the `Screen`. 
```
class Screen{ 
public:
	//display overlaoded on whether the object is const or not
	Screen &dispaly(std::ostream &os)
		{ do_display(os); return *this;}
	coinst Screen &display(std::ostream &os) const
		{ do_display(os); return *this;}

private:
	// function to do the work of displaying a Screen
	void do_display(std::ostream &os) const { os << contents;}
	// other members as per
};
```
When one member points to another, `this` pointer is passed implicitly. 
When display calls `do_display`, its  own `this` pointer is implicitly passed to `do_display`. 
The non`const` version of `display` calls `do_display`, its `this` pointer is implicitly converted from a pointer to non`const` to a pointer to `const`. There is a conversion here. 

Whether the object is `const` will determine which of the member functions will be displayed. 

```
Screen myScreen(5,3);
const Screen blank(5,3);
myScreen.set('#').display(cout); // calls non const version
blank.display(cout); // calls const version
```

TOP TIP: 
	Use private utility functions for common code. 
		It might not be obvious as to why we defined a separate do_display operation. After all, the calls to `do_display` aren't much simpler than the action done in `do_display`. 
		-  A general desire to avoid writing the same code in more than once place.
		- We expect that the `display` operation will become more complicated as our class evolves. As the actions involved do obfuscate, it makes more obvious sense to write those actions in one place, not two. 
		- If only one function exists, then the debugging will be easier. 
		- There needn't be any overhead involved in this extra function call. The `do_display` will be implicitly `inline`. There will likely be no run-time overhead associating with calling `do_display`


In practice, there are lots of instances, where functions will call other functions in order to do their dirty work. 

