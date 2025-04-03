#universalreferences
If we want to overload universal references, it's rough to do it normally, as Universal References are greedy in the function matching. 
One of the ways to do it, is the template taking in the universal reference (which has to be a template), will then pass on the argument to whatever, using `std::forward`. 






Here it is without a universal reference, this is just with the idea to present:
```
void printOut(const string& s1, std::false_type)
{ 
	cout << "False: " << s1 << endl;
}

void printOut(const string& s1, std::true_type)
{ 
	cout << "True: " << s1 << endl;
}

int main(){
	string n; 
	printOut("Hugo", is_integral<string>());
}
```


There is the idea of `std::conditional`
[std::conditional](https://en.cppreference.com/w/cpp/types/conditional)

Member typedef type, which is defined as T if B is true at compile time (not run time like the book that uses the above example) or F if B is false. 
The usual is using: 
```
template<bool B, class T, class F> 
using conditional_t = typenae conditional<B,T,F>::type;
```

The possible implementation: 
```
template<bool B, class T, class F>
struct conditional { using type = T; }

template<class T, class F> 
struct conditional<false, T, f> { using type = F; }
```

Usage:
```
// Type1 is int
using Type1 = std::conditional<true, int, double>::type;

// Type2 is double
using Type2 = std::conditional<false, int, double>::type;

// obviously int is smaller
// Type3 is double 
using Type3 = std::conditional<sizeof(int) >= sizeof(double), int, double>::type;
```


Our simple usage: 
```
#include <iostream>
#include <type_traits>
#include <utility>

struct LValueTag {};
strust RValueTag {};

// Primary template to select the appropriate tag
template <typename T> 
struct ValueCategoryTag { 
	using type = std::conditional_t<std::is_lvalue_reference<T>::value, LValueTag, RValueTag>;
};

// function implementations for lvalues 
template <typename T>
void process_impl(T&& value, RValueTag)
{ 
	// do a thing with lvalues
}

void process_impl(T&& value, RValueTag)
{
	// do a thing with rvalues
}

// Dispatch function that selects the correct implementation based on the tag
template <typename T> 
void process(T&& value){ 
	using TagType = typename ValueCategoryTag<T&&>::type;
	proces_impl(std::forward<T>(value), TagType());
}

// making it as easy as possible in main
int main(){ 
	int x = 42; 
	process(x); 
	process(42);
	return 0;
}
```


```
struct SlowAlgorithmTag{};
struct FastAlgorithmTag{};

tempate <typename T> 
void performTask(T tag);

template <>
void performTask(SlowAlgorithmTask)
{ 
	// do something
}

template <>
void performTask(FastAlgorithmTask)
{ 
	// do something
}

int main() 
{ 
	performTask(SlowAlgorithmTask{});
	performTask(FastAlgorithmTask{});
}
```
We aren't doing anything with those objects, however, we are resolving the overload in order to get the right performance. 
This is just resolving the overloaded set, statically, with static types. 

#### Using SFINAE
Imagine that we are going to choose a function depending on whether a copy is costly or not. 
We will be using structs as tags in order to determine which implementation to select based on the size of the type. 


```
struct CheapToCopyTag{};
struct ExpensiveToCopyTag{};

// helper type trait to determine copy cost
template <typename T> 
struct CopyCost 
{ 
	using TagType = std::conditional_t<(sizeof(T) <= sizeof(void*)), 
		CheapToCopyTag, ExpensiveToCopyTag>;
};
// very nice indeed

// primary function template declaration
template <typename T> 
void processData(const T& data, CheapToCopyTag)
{ 
	// do the thing
}

template <typename T> 
void processData(const T& data, ExpensiveToCopyTag)
{ 
	// do the thing
}

// Dispatcher function 
tempalte <typename T> 
void processData(const T& data)
{ 
	processData(data, typename CopyCost<T>::TagType{});
}
```

Here we are using the idea of a dispatcher function in order to create an interface that will disperse automatically. 

