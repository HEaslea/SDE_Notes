The whole point of this, is to make sure that templates are instantiated in certain places, at certain times, with types that we know will be used throughout. 
The increased compile times as the compiler has to automatically instantiate etc. can ride high. 

#### Explicit Instantiation Declaration : `extern template`
Tells the compiler not to instantiate a particular template in the current translation unit (source file). Declaring that it will be defined elsewhere. 
###### Syntax
```
extern template class ClasasName<TemplateArguments>;
extern template ReturnType FunctionName<TemplateArguments>(Parameters);
```

#### Explicit Instantiation Definition : `template`
Forcing the compiler to instantiate the template in the current translation unit, generating the code for that specific template specialization right there. 
###### Syntax
```
template class ClassName<TemplateArguments>;
template ReturnType FunctionName<TemplateArguments>(Parameters);
```

### Why Use It?: 
1. Reduce Compilation Times: By instantiation a template in a single translation unit, you avoid redundant instantiations across multiple source files. Essentially we are grouping up all the instantiations, into a file. 
2. Control Binary Size: Preventing multiple definitions of the same template instantiation across different obj files, which can reduce the size of the final binary. 
3. Separating Interface from Implementation: This is pretty obvious. 

### The Fix
```
// MyClass.h
#pragma once

#include <vector>

class MyClass
{ 
public: 
	void doSomething();
};
```

```
// MyClass.cpp

#include "MyClass.h"
#include <vector> // I'm Pretty Sure You Don't Need This

// Explicit Instantiation Defintiion
// You know I didn't think of this, but stl containers do this too 
template class std::vector<MyClass>;
```

```
// OtherFile.cpp
#include "MyClass.h"
#include <vector>

// this means that the definition is elsewhere, don't instantiate in here
extern template class std::vector<MyClass> 

void anotherFunction()
{ 
	std::vector<MyClass> vec;
	// usees the instantiation defined in MyClass.cpp
}
```


### Practical Example
```
// MyTemplate.h
#pragma once

#include <iostream>

template <typename T> 
class MyTemplate 
{ 
public: 
	void display(const T& value);
};
```

```
// MyTemplate.cpp
#include "MyTemplate.h"

template <typename T> 
void MyTemplate<T>::display(const T& value)
{ 
	cout << value << endl;
}

// Explicit Instantiation Definitions
template class MyTempalte<int>;
template class MyTempalte<double>;
```

```
// main.cpp
#include "MyTemplate.h"

// Explicit Instantiation Declarations
extern template class MyTempalte<int>;
extern template class MyTempalte<double>;

int main() 
{ 
	MyTemplate<int> intObj;
	intObj.display(42); // Using the instantiation from MyTemplate.cpp

	MyTemplate<double> doubleObj;
	doubleObj.display(3.14); // Using the other instantiation in MyTemplate.cpp
}
```

### The Drawbacks
- **Consistency**: You have to make sure that there are the same instantiations across all translation units, mismatches can lead to linker errors or undefined behaviour. 
- **Separate Compilation**: Most beneficial when dealing with large projects, with many source files, in small projects, the benefits are minimal. 
- **Visibility**: The translation unit containing the explicit instantiation definitions must be linked with all other translation units that use the instantiated templates. 
- **Specializations**: This works for both primary templates and their specializations, not really a drawback, but it works with both. 


### When Not To Use: 
- When there are few instantiations: the overhead of managing that might outweigh the benefits of actually using it. 
- Header Only Libraries: When we don't have any source files to contain the instantiation definitions. 
