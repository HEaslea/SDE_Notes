### Pointers to Implementation: 
The idea here is that if we have a struct: 
```
class Widget{ 
public: 
	Widget(); 

private: 
	string name; 
	vector<double> data;
	Gadget g1, g2, g3;
};
```

The main issue here is that we have data members of a type that has to be complete, meaning that we have to have included the header file that contains the definition of Gadget. 

If that header's content changes a lot, then there will be a lot of re-compiling. 

String and Vector don't change very often. However, if Gadget does, then the time for recompiling will be ridiculous, very often. 

```
class Widget{ 
public: 
	Widget(); 
	~Widget();

private: 
	struct Impl; // declare implementation struct 
	Impl *pImpl; // and pointer to it
};
```

Here, therefore we no longer need to including string vector, and gadget. 

Here we have some incomplete types. `Widget::Impl` here is an incomplete type. 

There are few things that you can do with a type like this, but you can declare a pointer, reference and a static object of them. 

Another part of this is that we have to dynamically allocate and deallocate the object that holds the data members, that used to be in the original class. 

Declaring a struct that is inside here, and then pointing to it. 
We are sort of packaging, so that we can have an incomplete type. 

eg. 

```
// in widget.cpp

#include "widget.h"
#include "gadget.h"
#include <string> 
#include <vector> 


struct Widget::Impl{ 
	std::string name; 
	std::vector<double> data;
	Gadget g1, g2, g3;
};

Widget::Widget() : pImpl(new Impl)
{} // this has to be this way

Widget::~Widget()
{ delete pImpl; }
```

This is the old way of doing this: 
Let's get a `unique_ptr` in the mix here. 



```
class Widget{ 
public: 
	Widget(); 
	... 

private: 
	struct Impl; 
	std::unique_tr<Impl> pImpl;  // using smart pointers instead now
};
```


```
// in widget.cpp
#include "widget.h"
#include "gadget.h"
#include <string>
#include <vector>

struct Widget::Impl{ 
	string name;
	vector<double> data;
	Gadget g1, g2, g3;
};

Widget::Widget()
: pImpl(std::make_unique<Impl>())
{}
```

No destructor. 

HOWEVER: 

```
#include "widget.h"


Widget w; // ERROR ERROR ERROR
```

There will generally be an error on the idea of applying `sizeof` or `delete` on an incomplete type. 

The error arises when w is destroyed, ie goes out of scope. The destructor is called, we didn't declare one, therefore the synthesized destructor goes into the unique_ptr. The default deleter will use delete on the raw pointer inside the unique_ptr. 
Prior to use delete, implementations typically have the default employ C++11's `static_assert` to ensure that the raw pointer doesn't point to an incomplete type. 

That is why we get an error. 

To fix, we just need to make sure that at the point that we are deleting, `Widget::Impl` is a complete type. It will become complete when we have the definition and it is seen. 


The definition that we want for our destructor now is this, where our type is seen before it is destroyed so that within the destructor, the `static_assert` for complete types is passed:
```
// in widget.cpp
// before, we didn't have the definition for our destructor defined here
// we just let it synthesize and i'm not sure how that is "defined"

// so all we need to do is "define it"

Widget::~Widget()
{}
```

Even the default synthesized one works, it's just it's definition for a complete type that we were missing: 
`Widget::~Widget() = default;` would work fine. 


Remember that the declaration of a destructor prevents compilers from generating the most operations. 
Therefore if you want that, then you have to define them yourself. 

However, you cannot do this : `Widget(Widget&& rhs) noexcept = default;`
Within the header file of declarations, meaning that we won't have seen the definition. 
The compiler generated move assignment operators needs to destroy the object 
In the header file, we are still pointing to an incomplete type. 
Therefore we just need to define them, in the same way after the destructor. 
The idea here is that in the header file, we are pointing to an incomplete type, however, in the cpp we are not. 

Remember that in the main here we have this: 
```
#include "widget.h"

Widget w;
```

**The key to success is to have the compiler see the body of Widget's Destructor, where it will generate destructor code, after the incomplete type has been defined, becoming complete.**
So the issue is where the destructor and move operations are defined, before or after the incomplete type. 


