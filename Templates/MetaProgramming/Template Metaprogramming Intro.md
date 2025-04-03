[Video](https://www.youtube.com/watch?v=VBI6TSo8Zog&list=PLWxziGKTUvQFIsbbFcTZz7jOT4TMGnZBh)

```
#include <iostream> 
#include <type_traits> 

template<typename T> // no specialization at all 
struct is_pointer { 
	static constexpr bool value = true;
};

template <typename T> 
struct is_pointer<T*> {  // template specialization for pointers to T
	static constexpr bool value = true;
};

template<typename T> 
void pritn1(T t){
	// print what we are pointing to if T is deduced as pointer to T
	if constexpr(is_pointer<T>::value)  // if check at compile time
	{ 
		std::cout << *t;
	}

	else { 
		std::cout << t;
	}
}

template<typename A, typename B, typename C> 
void print3(A a, B b, C c)
{ 
	print1(a);
	std::cout << "  " << std::endl;
	print1(b);
	std::cout << "  " << std::endl;
	print1(c);
	std::cout << "\n" << std::endl;
}
```




Going a step further to see if we can strip T of it's pointer and get the type of that which we are pointing to:
```
template<typename T> // no specialization at all 
struct is_pointer { 
	static constexpr bool value = true;
};

template <typename T> 
struct is_pointer<T*> {  // template specialization for pointers to T
	static constexpr bool value = true;
};

template<typename T> 
struct strip_pointer{ 
	using type = T;
}

template<typename T>  // again template specializtion to return T
struct strip_pointer<T*>{ 
	using type = T;
}

template<typename T> 
void print1(T t){
	using T_without_pointer = strip_pointer<T>::type;

	if constexpr(is_pointer<T>::value && std::is_floating_point<T_without_pointer>::value){
		std::cout << *t;
	}
	else { 
		std::cout << t;
	}
}
```
![[Pasted image 20240723132321.png]]

Both our inputs and out outputs are types in this sense. 

[Video2](https://www.youtube.com/watch?v=_yqIdYBdyPo&list=PLWxziGKTUvQFIsbbFcTZz7jOT4TMGnZBh&index=2)


