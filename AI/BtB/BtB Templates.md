#templates
[[Template Constraints]]
[Video in Reference](https://www.youtube.com/watch?v=HqsEHG0QJXU)
[[Specialized Templates]]
##### Providing Generic Code for Arbitrary Types/Values
Writing code for something that is not clear yet, the type, the value, etc. etc. 

### Function Templates
- Generic function code for arbitrary types
```
template <typename T> 
T mymax(T a, T b)
{
	return b < a ? a : b;
}
```

Saying let's assume that we have a type T. 

`typename` can be replaced by `class`. Doesn't mean it has to be class. 

We don't have to use `T` either, it's just a placeholder. 

```
int i1 = 42, i2 = 77;
cout << mymax(i1, i2);
```

This is the point that it becomes real code and will compile. 
It will compile as this: 
```
int mymax(int a, int b){ 
	return b < a ? a : b;
}
```

When we do something like this: 
```
cout << mymax(0.7, 33.4);
```
We will compile: 
```
double mymax(double a, double b){ 
	return b < a ? a : b;
}
```

And the same for if we did something like this: 
```
string s{"hi"}, t{"world"};
cout << mymax(s, t);
```


We can even do something like this: 
```
vector<int> a{0,1,2,3}, b{4,5,6,7,8,9};
auto f = mymax(a, b); // where f is b
```


So realistically a function template denotes conceptually a group of functions, that will compile to a certain type when we pass in a certain type. 

We can even parameterise the type, and explicitly say the type using angled brackets: 
```
s = mymax<string>("hi", "ho"); // template instantiation
```
Template instantiation used here, like with vectors. 

### Generic Iterating
Iterate over elements of different containers: now the code to do that is always the same: 
```
// printing all elements for any container (roughly)
for(const auto& elem : container) { 
	cout << elem << '\n';
}
```
However, with unspecified container types: eg. a vector, linked list, binary tree, map, hash table (unordered map). 
```
template<typename T> 
void print(const T& container){ 
	for(const auto& e : container){ 
		cout << container << '\n';
	}
}
```

Now, the containers might change, and the typing of the elements in the containers too: 
```
std::vector<int> v; 

std::set<std::string> s; 

std::vector<double> v2; // would compile different code to v
```

### Templates in Header Files
Templates are usually defined in header files: 
- not only declared
- no `inline` necessary
mycode.hpp
```
template<typename T>
T mymax(T a, T b){ 
	return b < a ? a : b;
}
```

```
#include "mycode.hpp"
...

int i1 = 42, i2 = 77;
auto a = mymax(i1, i2); // OK
auto b = mymax(0.7, 33.4); // OK too

string s{"he"}, t{"ho"};
auto c = mymax(s, t); // OK as well
```

![[Pasted image 20240328150651.png]]


### `auto` Parameters for Ordinary Functions
We can write: 
```
template<typename T> 
void printColl(const T& coll)
{ 
	for(const auto& elem : coll){ 
		cout << elem << '\n';
	}
}
```

Or we could write something like this: 
```
void printColl(const auto& coll){ 
	for(const auto& elem : coll){ 
		cout << elem << '\n';
	}
}
```
we let the compiler find out the type itself. It's so much more convenient, this is still a function template, it is the same thing. Except here that we do not have a name for `T`. We cannot use that type, of `T` explicitly, therefore if we want to use the type explicitly, it's a little difficult. 

Again, with templates, belongs in header files, no inline needed. 

### Function Template Requirements
- Template require that all **operations are supported**. 
```
template<typename T> 
T mymax(T a, T b){ 
	return b < a ? a : b;
}
```

The second requirement is that here, we are passing by value, the type must be copyable !!!!!!
Always thing about implicit requirements. 
Either with a move or copy constructor. 

if we said something like `cout << mymax(7, 33.4`); // Error can't deduce T (int or double)
we can fix using `cout << mymax<double>(7, 33.4);` // OK T is double

To note here that complex values do not support the `<` operator. 
```
std::complex<double> c1, c2; 
mymax(c1, c2); // Deduces T as complex<>, but no < supported here
```

Now, another one is atomic ints, they **cannot be copied and cannot be moved**: 
```
std::atomic<int> a1{8}, a2{15};
mymax(a1, a2); // ERROR passing by value here is rough, copying of atomic<> is disabled
```

How might we explicitly tell the user the requirements that are necessary of the type that we are passing in, as imagine that in this function, there are 100000 lines, and there are other functions being called, and those might call other things and use operators in such a way etc. etc. 

In order to do this: we use **CONCEPTS**

### Concepts
#concepts 
Constraining templates: 
```
template<typename T> 
requires std::copyable<T> && SupportsLessThan<T> // explicit constraints
```

The `SupportsLessThan` here is actually user defined: 
```
template<typename T>
concept SupportsLessThan = requires (T x) { x < x; }; // our named requirement
```

![[Pasted image 20240329163916.png]]
When we call that template, we require certain "things" of our objects/type. 

### Multiple Template Parameters
```
template<typename T1, typename T2>
void print(const T1& val1, const T2& val2)
{ 
	cout << val1 << " " << val2 << '\n';
}
```
No longer require that they have the same type, they can be, but they don't have to be anymore. ![[Pasted image 20240329164123.png]]
There is an interesting problem: think about `mymax`, it returns the type `T`. However, if we have multiple template types, what is the return type here? 
What is the best type to return here, so that we don't lose any information. 
Every expression has a type; and the rule here, for the compiler, is that here, with our ternary condition expression, the `return b < a ? a : b;`, it will try to find the common type. 
You already thought of it on writing, the best way to do this is let the compiler deduce, so that we can just use `auto`. 
![[Pasted image 20240329164609.png]]

```
template<typename T1, typename T2> 
auto mymax(const T1& val1, const T2& val2){ 
	return b < a ? a : b;
}
```

Let the compiler do the work. 

These are considered the basics lol, pretty basic: 
There is more to come :) :) :)

```
int i = 45;
if(static_cast<double>(i)){ 
cout << "FINE << endl;
} else { 
	cout << "NOT FINE" << endl;
}
```


# Class Templates
```
#include <vector> 

template<typename T> // type T, will be used as elements in the vector
class Stack { 
	private: 
		std::vector<T> elems;
	public: 
		Stack(); //  default constructor
		void push(const T&); // push
		T pop(); // pop
		T top() const; 
		bool empty() const { 
			return elems.empty(); // using vector container function to return bool if our user defined stack is empty
		}
};
```
The thing with classes, you have to specify the template parameter. 
eg. `Stack<int> intStack;`
`Stack<std::complex<double>> cpxStack;`

![[Pasted image 20240329165744.png]]
If we choose not to inline. 

### Class Template Arguments

```
template<typename T> 
class Stack{ 
private: 
	std::vector<T> elems; // elements 
	...
public: 
	void Print() const{ 
		for(const T& elem : elems){ 
			std::cout << elem << " "; // requires << for element type T
		}
	}
};
```

Does this mean that we can only have a stack of types that only support the `<<` operator. NO we can still instantiate an object that doesn't support `<<` however, we just have to not call `Print()`. 

Say if we had a `stack<pair<..., ...>>`, Then we would not be able to `Print()`, as pairs do not have a `<<` operator. However all the rest is fine. 

![[Pasted image 20240329170841.png]]


### Template Explicit Specializations
[[Specialized Templates]]
Consider a template class: 
```
template<typename T> 
class TemplateClass{ 
};
```
Now imagine that you want to provide a specialized implementation of `TemplateClass` for a specific type, say `int`. You can achieve this using explicit specialization: 
```
template<>
class TemplateClass<int>{ 
};
```

Here `template<>` precedes the specialization of the function template `TemplateFunction` for the type `char`. 
```
template<typename T> 
void TemplateFunction(T value){ 
	// Function definition
}

template<> 
void TemplateFunction<char>(char value){ 
	// specialized implmentation of char
}
```



