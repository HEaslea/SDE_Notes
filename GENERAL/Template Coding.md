#### Repository For Template Based Code
You can assume `using namespace std;`

```
// template alias
template <typename T> 
using IsIntegral = std::is_integral<T>;

template <typename T, typename = std::enable_if<IsIntegral<T>::value>
void isInt(T a)
{ 
	cout << " IS INT " << endl; 
}

// for non integral types
template <typename T, typename = std::enable_if<!IsIntegral<T>::value>
void isInt(T a)
{ 
	cout << " NOT INT " << endl;
}
```


```
// A C++17 approach

template <typename T> 
void isInt(T a)
{ 
	if constexpr(IsIntegral<T>::value)
		cout << " IS INT " << endl;
	else 
		cout << " NOT INT " << endl;
}
```

What's nice about this is that we have no ambiguity, and the whole thing is contained in one function, and `std::enable_if` is not dirtying the signatures. 


```
// C++20 Concepts Very Cool Indeed
template <typename T> 
concept Integral = std::is_integral_v<T>; // Concept that is basically a bool

void isInt(Integral auto a)
{ 
	cout << " IS INT " << endl;
}

template <typename T> 
requires (!std::is_integral_v<T>)
void isInt(T a)
{ 
	 cout << " NOT INT " << endl;
}
```

#### Generic Containers Using Templates
Custom Vector class
```
template <typename T> 
class GenVector
{ 
	T* data;
	size_t size_;
	size_t capacity_;

	// manips
	void reserve(size_t newCap)
	{ 
		if(newCap <= capacity_) return;

		T* newData = new T[newCapacity]; // create new array in new memory
		for(size_t i = 0; i < size_; ++i)
			newData[i] = data_[i];
		delete[] data_;
		data = newData;
		capacity_ = newCapacity;
	}


public: 
	// Creators
	GenVector() : data_(nullptr), size_(0), capacity_(0) {}
		// ... others
		
	// Deleters
	~GenVector() { delete[] data_; }
		
	// Manips
	void push_back(const T& value)
	{ 
		if(size_ >= capacity_)
		{ 
			reserve(capacity_ == 0 ? 16 : capacity_ * 2);
		}
		data_[size_++] = value;
	}

	// Accessors
	T& operator[] (size_t index)
	{ 
		assert(index < size_);
		return data_[index];
	}
};
```

#### Generic Pool Allocator
```
template <typename T>
class ObjectPool 
{ 
	std::vector<std::unique_ptr<T>> pool_;
	std::stack<T*> freeList_;

public: 
	// Creators
	// Acting as a default ctor
	ObjectPool(size_t initialize = 100)
	{ 
		for(size_t i =0; i < initialSize; ++i)
			pool_.push_back(std::make_unique<T>());
	}
	
	// Manips
	

	// Accessor
	T* acquire() 
	{ 
		if(freeList_.empty())
		{ 
			pool_.push_back(std::make_unique<T>());
			return pool_.back().get();
		}
		T* obj = freeList_.top();
		freeList_.pop();
		return obj;
	}

	void release(T* obj)
	{ 
		freeList_.push(obj);	
	}
};
```

```
class Entity 
{ 
	ID id_;
public: 
	using ID = unsigned int;

	Entity(ID id) : id_(id) {}

	ID getId() const { return id_; }
};

class ComponentManager
{ 
	template <typename T> 
	std::unordered_map<Entity::ID, std::unique_ptr<T>>& getComponentStorage()
	{ 
		// interesting way of doing it
		static std::unordered_map<Entity::ID, std::unique_ptr<T>> stroage;	
		return storage;
	}
	
public: 
	// potentially a forward reference here?
	template<typename T> 
	void addComponent(Entity entity, T component)
	{ 
		getComponentStorage<T>()[entity.getID()] = std::make_unique<T>(component);
	}

	template <typename T> 
	T* getComponent(Entity entity)
	{ 
		// gives us a ref tothe unordered map of the storage
		auto& storage = getComponentStorage<T>(); 
		auto it = storage.find(entity.getID());
		// if found
		if(it != storage.end()) 
			return it->storage.get(); // return the entity of it
		
		return nullptr; // Could instigate error messages when further down the line
	}

	template <typename T> 
	void removeComponent(Entity entity)
	{
		getComponentStorage<T>().erase(entity.getID());	
	}

};

int main() 
{ 
	ComponentManager cm; 
	Entity player(1);

	cm.addComponent(player, Transform{ Vector3(0, 0, 0), Quaternion(), Vector3(1, 1, 1));


	cm.addComponent(player, Renderable{ /* mesh data */ });

	Transform* transform = cm.getComponent<Transform>(player);
	if(transform) // remember can be a null ptr
		transform->position = Vector3(10, 0, 0);

	cm.removeComponent<Renderable>(player); // as we get ID 
}
```

The template instantiation for the `getComponentStorage` will have an unordered map for every type of function that is instantiated. 

When we add these: 
```
ComponentManager cm;

// Adding components with IDs of 1
cm.addComponent(1, Transform{ /* ... */ });
cm.addComponent(1, Renderable{ /* ... */ });
```
`cm.addComponent(1, Transform{ /* ... */ });` gives us the `unordered_map<Entity::ID, std::unique_ptr<Transform>>`

`cm.addComponent<renderable>(1, Renderable{ /* ... */ })` managed the `std::unordered_map<Entity::ID, std::unique_ptr<Renderable>>`. 

Here for each unique T: 
- one `static` `std::unordered_map<Entity::ID, std::unique_ptr<T>> storage` exists. 
There is one persistent `storage` map per type `T`. 



###### Some MetaProgramming
- Recursion in Templates
- Compile Time Computation - love it 
- Type definitions
```
template<unsigned int N> 
struct Factorial 
{ 
	static constexpr unsigned long long value = N * Factorial<N-1>::value;
};

template <> 
struct Factorial<0>
{ 
	static constexpr unsigned long long value = 1
};
```

Computing using structs, with a member variable as the value. 

This is something that confused me for a long time, however makes more sense now: 
```
template <typename T> 
struct isPointer 
{ 
	static constexpr bool value = false;
};

template <typename T> 
struct IsPointer<T*>
{ 
	static constexpr bool value = true;
};

// helper variable template
template <typename T> 
constexpr bool IsPointer_v = IsPointer<T>::value;
```

SFINAE time
```
template <typename T> 
typename std::enable_if<std::is_integral<T>::value , void >::type
printType(const T& value)
{ 
	cout << value << " Is an integral type" << endl;
}

// if floating point
template <typename T>
typename std::enable_if<std::is_floating_point<T>::value, void>::type
printType(const T& value)
{ 
	cout << value << " is a floating point type" << endl;
}
```

Some Variadics
```
template <typename ... Types> 
struct TypeList {};

template<std::size_t Index, typename List>
struct TypeAt;

// specialization
template<std::size_t Index, typename Head, typename ... Tail> 
struct TypeAt<Index, TypeList<Head, Tail...>>
{ 
	using type = typename TypeAt<Index - 1, TypeList<Tail...>>::type;
};

// base case when our Index 0
template <typename Head, typename... Tail> 
astruct TypeAt<0, TypeList<Head, Tail...>>
{ 
	using type = Head;
};

// with a helper alias template
template <std::size_t Index, typename List> 
using TypeAt_t = typename TypeAt<Index, List>::type;

using MyTypes = TypeList<int, double, char, std::string>;c

int main() 
{ 
	TypeAt_t<2, MyTypes> var = 'A';

	TypeAt_t<3, MyTypes> str = "Helo, TypeList!";
}
```
`TypeList<>` is a variadic that is just a type for a bunch of other types, doesn't need to contain anything, we just want the fact that we can write something along the lines of: `TypeList<int, char, string>`. 

```
template <typename, typename = void> 
constexpr bool is_iterable = false;

template <typename T> 
constexpr bool is_iterable<T, 
	std::void_t<decltype(std::declval<T>().begin()), 
				decltype(std::declval<T>().end()) >> 
= true;
```
I'm pretty sure `void_t<>` is similar to `enable_if` where we instantiate to void if the condition is correct. 

```
template <class, class = void> 
struct has_pre_increment_member : std::false_type {}

template<class T> 
struct has_pre_increment_member<T, 
				std::void_t<decltype(++std::declval<T&>())>
				> : std::true_type {}
```


```
#include <limits>

template <typenam T, 
	template <typename, typename = std::allocator<T>> class Cont, 
	typname = std::enable_if_t<std::is_integral<T>::value
	>> 
void MaxEle(const Cont<T>& cont)
{ 
	T max = std::numeric_limits<T>::min();
}
```


#### Sherlock Level Deduction
```
#include <iostream>

template <int N> 
class X 
{ 
public: 
    using I = int;
    void f(int)
    { 
    }
};

template <int N>
void fppm(void (X<N>::*p)(typename X<N>::I));

int main(){ 
    fppm(&X<33>::f);
}
```

First, the function template. 
A pointer to a member function `X<N>` with the signature `void (X<N>::*) (typename X<N>::I)`
The compiler needs to match the type of the argument (`&X<33>::f)` with the function parameter of `fppm`. 

The top level, the compiler sees that they are both pointers returning `void` and taking a single parameter. 
Then the compiler sees `X<N>::*` and `X<33>::*`, it deduces `N = 33` in order to make them match. 
Matching the Parameter type is easy: as due to the alias in the struct, `typename X<N>::I`  is equivalent to `int` for any `N`. 
Remember the deduced contexts idea: 
> "**Matching process proceeds from the top level constructs and recurses through the composing elements, this is deduced contexts**". 

The top level type here is `fppm` which is `void (X<N>::*p) (typename X<N>::I)`
We start by matching `void` at the first level. 
Then we have to look into the class template `X<N>`, that's where we deduce `N = 33`. 
Then the third level we look at the `I`, which is alias for `int`. Therefore they match, quite simply. 

```
Function Template Parameter:
void (X<N>::*p)(typename X<N>::I)

Argument Passed:
void (X<33>::*)(int)

Deduction Steps:
1. Match the overall pointer to member function types:
   - Return Type: void == void
   - Parameter Type: X<N> vs. X<33>
   
2. Deduce N:
   - X<N> must match X<33> → N = 33
   
3. Match the member function parameter types:
   - typename X<N>::I vs. int
   - X<33>::I is int → int == int (matches)
```

The `typename` here is key, as the `x<N>::I` is a dependent type, depending on `N`. 
Informs the compiler that `I` is not a static member of `X<N>`  for all `N`. 

##### Template Metaprogramming SQRT
```
// This is effectively a binary search
template <typename T> 
constexpr T sqrt(T x)
{
	if(x <= 1)
		return x;

	T lo = 0, hi = x;
	for(;;)
	{ 
		// mid is an int I believe
		auto mid = (hi + lo)/2, midSquared = mid * mid;
		if( lo + 1 >= hi || midSquare == x)
		{ 
			return mid;
		}

		if(midSquared < x)
		{ 
			lo = mid; 
		} else { 
			hi = mid;
		}
	}
}
```
**Monotonic functions**- either always increasing, or always decreasing, never the other, they never swap on that front. 

This function here uses this property: where `f(y) = y^2`, will always increase if `y >= 0`. 
This, somehow, means that we can apply a binary search to solve this. 

Then we know that If $y_1 < y_2$ , then $y_1^2 < y_2^2$ . 
Then we use something called the bisection method, allowing us to find roots for continuous functions. 

The function here really is just, $f(x) = x^2$. 
It repeatedly bisects an interval and selects a subinterval where the function changes sign, thereby narrowing down the location of the root. 
Generally the bisection method lends itself to the binary search method very nicely, they look almost identical. 

The bisection method : [Bisection Method](**https://en.wikipedia.org/wiki/Bisection_method**)
![[Pasted image 20241011231035.png]]
The mother of the binary search method? 

Notice the $f(a)$ and $f(b)$ have to be opposite signs in order for this to work. 
The general idea here is that we take `(a + b) / 2` in order to get the point in the middle. Then we have  $f(c)$. If c is a root, therefore $f(c) = 0$ . If not, then there are two possibilities. Either $f(a)$ and $f(c)$ have opposite signs or $f(b)$ and $f(c)$ have opposite signs.
If $f(a)$ and $f(c)$ have opposite signs, then we take b to be $f(c)$. Basically, all we are saying is that the root must occur then between these two, and absolutely not between the other two, which have the same sign. 

![[Pasted image 20241011231801.png]]


