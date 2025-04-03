	 A non-type template argument, is a constant value or expression that you can pass to a template as an argument, other than a type. 
 Allowing us to use values that are known at compile time parameterize templates, which enables template metaprogramming. 
 They must be known at compile time, so you can use literals, `constexpr` variables, or expression that the compiler can evaluate at compile time. 

Remember that a const expression needs a series of constants in order to make sure that it is known at compile time: 
```
int x = 5, y = 1; 
constexpr int a = y + x; // ERROR as x and y are not constants

const int a = 5, b = 1;
constexpr int z = a + b; // fine as both are const expressions
// they csan be evaluated at compile time.
```

Non-type template arguments here: 
```
#include <iostream> 

template<int N> 
void printNTTA(){ 
	cout << "The value of the non-type template arg is " << N << endl;
}

int main(){ 
	printNTTA<5>(); // output here is 5
	constexpr int value = 10;
	printNTTA<value>(); // output here is 10
}
```

You can see more clearly now that everything we do in relation to this, is going to be done at compile time. 

This is similar to specialized implementation: or template specialization (for a certain type). 

```
// generic function template
template<typename T> 
void MyFunc() { 
	cout << "Generic Implementation" << endl;
}

template<> void MyFunc<int>() { 
	cout << "Specialized implementation for int" << endl;
}

int main(){ 
	MyFunc<double>(); // calls the generici implementation
	MyFunc<long>(); // calls generic version
	MyFunc<int>(); // Calls the spec version
}
```

