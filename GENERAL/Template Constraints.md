#concepts
Take the idea of this: 
```
template <typename T>
T add(const T& a, const T& b)
{ 
	return a + b;
}
```

Now here, we expect that the types that are passed in, can be added together, eg `ints, floats` and even `strings`. 

Instead of using templates, we might with modern looking C++, do something that looks a little like: 
```
auto add(const auto& a, const auto& b){ return a + b; }
```
The compiler will generate a template function from this auto. However, this is not the exact same, the parameters in this one, do not have to be the same type, and that might throw a whole new error. 
If you added together a `string` and an `int` then you will get a syntax error, that is about no match for 'operator+' etc. This type of error would be in the generated template code that the compiler has created. 

[[Concepts]]

specifying constraints on templates: 
```
template <typename T>
requires integral<T>
	|| floating_point<T>
	|| same_as<T, std::string>
T add(const T&a, const T& b){ return a + b; }
```
#requires
The `requires` keyword will constrain the possible parameters to integer, float, double, or string. 

This will also help get us a better compiler error. 
Therefore if we passed in two `Dog`, we would get error's pointing to the add method. 

However, what if we wanted to do our own objects. 
Let's say we have colours, they are "addable" meaning that they have the ability to use the + operator in  the class. 
So how do we constraint that, for types that are "addable", instead of going through each type. 

```
template<typename T>
concept addable = requires (T v) { 
	{ v + v } -> same_as<T>;
};
```
The `concept` here is a predicate that describes the requirement for a template argument. Here `addable` is a concept that specifies the requirement for types that support addition.

Then `requires` will define more the requirements for types that satisfy the `addable` concept. 
Then in the clause, we specify what operations or expressions must be valid for types that satisfy this concept. 
Then `{ v + v } -> same_as<T>;` is a requirement expression. It will check that the expression `v + v` adding two types of T together is well-formed and results in a type that is the same as T. The `-> same_as<T>` specifies the expected return type of the expression. 
Another similar vein would be
```
template <typename T>
concept Multipliable = requires (T a){ 
	{ a * a } -> std::same_as<T>;
}
```
Again, that's pretty standard. 
Remember here that we have to `#include <concepts>`, I really like the idea though, very fun. 

```
template <typename T>
concept HasSerialize = requires (T obj){ 
	{ obj.seralize() } -> std::same_as<void>; // return type void
}

// my own class here that can serialize
class MyClass{ 
	void serialize(){ 
		std::cout << "Serializing whoo durr *machine noise*" << std::endl;
	}
};

// another 
struct AnotherClass{ 
	// no serialize member fucntion 
};

int main(){ 
	MyClass Obj; 
	AnotherClass Obj2;

	if constexpr (HasSerialize<MyClass>){ 
		obj1.serialize();
	} else { 
		std::cout << "MyClass does not have serialize()" << std::endl;
	}

	if constexpr (HasSerialize<AnotherClass>){ 
		obj2.serialize();
	}else { 
		std::cout << "Another class does not have serialize()" << std::endl;
	}
}
```
It seems that this is super new and you can't actually do it on your compiler atm lol. GLHF



here's the video that explains all of it: 