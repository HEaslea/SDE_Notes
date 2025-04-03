## Headers and Sources
Header should contain all the declarations, so that when we include them into source file, we can use those functions, classes etc. And the Linker will provide the definitions when the time is right. 

The source files, are compiled separately into obj files, then they are all linked together at the end. 

This separation facilitates **encapsulation**, **modularity**, and **reusability**, allowing different parts of a program to interact through well-defined interfaces.

### For Non Templates 
(boo boring). 
The trick is to remember that when we declare members  in a class, we are defining that class. It is being defined, however, the compiler will treat these differently, therefore it doesn't really matter what happens to them. This is weird, however, think of the classes as the outer layer, when we declare members in them, (not define members), we are defining what the class is, not what it's elements do and are. 
###### Header Files
###### Classes:
- Declarations: 
	- Class or struct <u>definitions</u> 
	- Member function declarations
	- Member Variable declarations. 
	- Nested Types, enumerations, and type aliases. 
	
	
```
// MYCLASS.h

#pragma once
// or
#ifndef MYCLASS_H
#define MYCLASS_H

#include <string> // or if you have a pch that's different

class MyClass
{ 
	// see the large scale C++ software design here 
	// remember that member variables should always be private
	int d_value;
	std::string d_name;
	static int s_counter; // static members too

	void helperFunction();

public: 

	// creators
	MyClass(); 
	~MyClass();

	// Manipulators
	void doSomething();

	// Accessors
	int getValue() const; // don't forget the const here
};

#endif
```

- Definitions: 
	- Implementation of member functions.
	- Initialization of static member variables (if any).
```
// MYCLASS.cpp
#include "MYCLASS.h"
#include <iostream>

int MyClass::s_counter = 0;

MyClass::MyClass() : d_value(0), d_name("Default") {}

MyClass::~MyClass() {}

void MyClass::doSomething() 
{ 
	// do something
}

int MyClass::getValue() const 
{ 
	return d_value;
}

void MyClass::helperFunction() 
{ 
	// helper implementation
}
```

###### Functions 
These are the simplest: 
```
// Utilities.h
#pragma once

int add(int a, int b);
```

```
// Utilities.h
#include "Utilities.h"

int add(int a, int b)
{ 
	return a + b;
}
```


###### Variables
[Extern](https://learn.microsoft.com/en-us/cpp/cpp/extern-cpp?view=msvc-170)
Specifying External Linkage - Specifying that the variable or function is defined in another translation unit, there has to be one definition somewhere. 
Remember that Global `const` variables have internal linkage by default, which is similar to using `static` as `const` which is somewhat like C# in that a `const` member variable is implicitly `static`. 

Remember that if we do not explicitly say what linkage we are using, then by default: 

`extern` - for non-`const` symbols
`static` - for `const` symbols. 

Get this tattooed on your forehead. 

###### Enumerations and Type Definitions
```
// types.h
#pragma once

enum class Colour
{ 
	RED, 
	GREEN, 
	BLUE
};

using StringList = std::vector<std::string>;
```

### Templates 
Templates are compile time constructs. 
The definition of the template code must be fully visible to instantiate and substitute. 
Meaning, that unlike non-template code, you cannot compile template definitions into separate object files and link them unless using explicit instantiation, which has its limitations.

Typically both declaration and definition are found within the header file: 
```
// MyTemplateClass.h
#pragma once

#include <vector> 

template <typename T> 
class MyTemplateClass 
{ 
	std::vector<T> d_items;
public: 
	MyTemplateClass();
	void add(const T& item);
	T get(int index) const;
};

// Then the definitions
template <typename T> 
MyTemplateClass<T>::MyTemplateClass() {}

template <typename T> 
void MyTemplateClass<T>::add(const T& item) {
	items.push_back(item);
}
```

Another option is to separate implementation file included in Header:  
```
// MyTemplateClass.h

#pragma once

#include <vector> 

template <typename T> 
class MyTemplateClass 
{ 
	std::vector<T> d_items;
public: 
	MyTemplateClass(); 
	void add(const T& item);
	T get(int index) const;
}

// This is the important bit
#include "MyTemplateClass.tpp"
```

```
// MyTemplateClass.tpp
#pragma once

template <typename T> 
MyTemplateClass<T>::MyTemplateClass () {}

template <typename T> 
void MyTemplateClass<T>::add(const T& item)
{ 
	d_items.push_back(item);
}

template <typename T> 
T MyTemplateClass<T>::get(int index) const 
{ 
	returns items.at(index);
}
```

`.tpp` is the template implementation extension. 

For functions, can you put the whole function definition in the header. 


### Explicit Template Instantiation
- If you know what template instantiations you will have in advance, then you can explicitly instantiate them to reduce compile time. 
```
// MyTemplateClass.cpp
#include "MyTemplateClass.h"

// Template Definitions

template class MyTemplateClass<int>;
template class MyTemplateClass<double>;
```



#### Examples: 
```
// VectorUtils.h

#pragma once

#include <vector> 

template <typename T> 
T findMax(const std::vector<T>& vec);

// define inline
template <typename T> 
T findMax(const std::vector<T>& vec)
{ 
	if(vec.empty()) throw std::invalid_argument("Empty Vector");
	T max = vec[0];
	// there will be an algorithm for this
	for(const auto& item : vec)
	{ 
		if(item > max) max = item;
	}
	return max;
}
```