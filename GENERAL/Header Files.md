#headerfiles
Variables, functions, classes, and so on must be declared before they can be used. 

```
int x; // declare
x = 42; // use
```
Each name must be declared in every .cpp file in which it is used. 

When compiled, each .cpp file will be compiled independently. 

To minimize errors, `C++` will use `header files` to contain declarations. 

The idea is that you make the declarations in the `header files` and then `#include` that header in every `.cpp` file. 

How to declare a class and then use it in a different source file. 
`my_class.h`; containing a class definition, note that the definition is incomplete; the member function `do_something` is not defined, which is very important. 
```
// my_class.h
namespace N
{ 
	class my_class
	{ 
		public: 
			void do_something(); // just declared
			// not defined here
	}
}
```
Then we create an implementation file, typically a `.cpp` file or similar extension. We'll call it `my_class.cpp` and provide a definition for the member function. 
We'll need an `#include <iostream>` in this one, to pull in the declaration for `std::cout`. 

Quotes are used for header files in the same directory as the source file, and angle brackets are used for the standard library headers. 

Many standard library don't even have an extension.

In this implementation file, we might use a `using` statement to avoid having to write the same qualifying mention of `my_class` or `cout` or `N::` or `std::` every two seconds. 
DO NOT PUT `using` STATEMENTS IN YOUR HEADER FILES;
They just get copied to everything that does the `#include "headerfilewithusing"`; 

```
// my_class.cpp
#include "my_class.h" // header in local directory
#include <iostream> // header in standard library

using namespace N; 
using namespace std;

void my_class::do_something()
{ 
	cout << "Doing Something!" << endl;
}
```

Now we can use `my_class` in another .cpp file. 
We `#include` the header file so that the compiler pulls in the declaration. 
All the compiler will need to know is that `my_class` has a public member function called `do_something()`. 
```
// my_program.cpp
#include "my_class.h" // header in the local directory

using namespace N;

int main() // entry point
{ 
	my_class mc;
	mc.do_something();
	return 0;
}
```
After the compiler finishes compiling each .cpp file, into .obj files, it passes the .obj files to the linker. 
The linker then merges the obj files it finds exactly one defintion for `my_class`, it is in the .obj file produced for `my_class.cpp` and the build succeeds. 

### Including Guards
We could have an include guard or a `#pragma once` directive to ensure that they are not inserted multiple times into a single.cpp file. 
```
// my_class.h
#ifndef MY_CLASS_H // include guard
#define MY_CLASS_H

namespace N{ 
// etc. etc. 
}


#endif /* MY_CLASS_H */
```

### What to put in a header file
As this file might be included into multiple files, it cannot contain definitions that might produce multiple definitions of the same name. 
The following are **NOT ALLOWED**, considered very bad practice: 
- built-in type definitions at namespace or global scope
- non-inline function definitions, inline functions are fine
- non-const variable definitions
- aggregate definitions
- unnamed namespaces
- using directives

`using` might not be a huge error, however, think about how it would work if it were brought into every single `cpp` file. 

### Sample header file
```
// sample.h
#pragma once
#include <vector> // #include directive
#include <string>

namespace N // namespace declaration
{ 
	inline namespace P
	{ 
		// ... 
	}

	enum class colors : short { red, blue, purple, azure };

	const double PI = 3.14; // const and constexpr definitions
	constexpr int MeaningOfLife{42};
	constexpr int get_meaning()
	{ 
		static_assert(MeaningOfLife == 42, "unexpected!");	
		return MeaningOfLife;
	}
	using vstr = std::vector<int>; // type alias
	extern double d; // extern variable

	#define LOG // macro definition

#ifdef LOG // conditional compilation directive
	void print_to_log();
#endif

	class my_class // regular class definition
	{  // but no non-inline function definitions
		friend class other_class;
	public: 
		void do_something(); // definition in my_class.cpp
		inline void put_value(int i) { vals.push_back(i);}
	private: 
		vstr vals; // using from earlier
		int i;
	}

	struct RGB
	{
		short r{0}; // member initialization
		short g{0}; 
		short b{0};
	}

	template <typename T> // template definition
	class value_store
	{ 
	public: 
		value_store<T>() = default;
		void write_value(T val)
		{ 
			//... function definition OK in template
		}
	private: 
		std::vector<T> vals;
	};

	template <typename T> // template declaration
	class value_widget;
}
```


