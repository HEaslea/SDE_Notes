
Metaprogramming Technique to overload a function using different tag parameters. 

```
#define PRINT(expr) std::cout << expr << std::endl

struct tag1 {};
struct tag2 {};

auto f(int a, tag1 dummy) {
	std::stringstream s;
	s << a << " via tag1\n";

	PRINT(s.str());
}

auto f(int a, tag2 dummy) {
	std::stringstream s;
	s << a << " via tag2\n";

	PRINT(s.str());
}


int main()
{
	f(1, tag1{});
	f(1, tag2{});
	
```

### Boolean Tag
`std::` lib does have `true_type` and `false_type`, which are types not values, therefore they can be used as parameters for overloads. 

```
auto g(int a, std::true_type t)
{ 
	std::cout << a << " via true type" << std::endl;
}

auto g(int a, std::false_type t)
{ 
	std::cout << a << " via false type" << std::endl;
}

g(1, std::true_type{});
g(1, std::false_type{});
```

Types can be programmed at compile time, so therefore we can make use of `g()` at compile time. 

```
template <int i> 
auto isEven()
{ 
	if constexpr(i % 2 == 0)
		return std::true_type{}
	else 
		return std::false_type{}
}

// now the call g() will be based on wither input is odd or even: 
g(1, isEven<11>());
```

To note here that `if constexpr` will be conditionally followed at compile time, the branch that is not taken will be discarded completely, which is amazing for template metaprogramming. 

### Struct to Tuple
So this next part is not in the version of C++ that you are using for your compiler, however here we go; 

There is something such as structural binding, particularly for aggregate classes. 
Remember aggregate classes don't have user-defined constructors, base classes, virtual functions etc. 
Structs and Arrays are them. 

```
struct S{ 
	int m0; 
	float m1; 
	char m2;
};

// from ChatGPT
// trait to determine the size of a tuple/tuple-like element
namespace std{
	template<>
	struct tuple_size<S> : std::integral_constant<size_t, 3> {};
}





int main() { 
	S s{42, 3.14, 'a'}; // all of this is fine
	auto [m0, m1, m2] = s; // doesn't work in my version


}
```




###### Basic `std::tuple_size` Usage
```
int main() 
{ 
	std::tuple<int, double, char> myTuple(1, 3.14, 'a');
	std::cout << std::tuple_size<decltype(myTuple)>::value << std::endl; 
	// outputs 3
}
```

So what I'm gathering here is that `tuple_size` is actually its own struct, that gives us information on the type of tuple that we feed into it. 
Tuples are also a really great way to look at packing and unpacking of types. 

So then we can customize `std::tuple_size` for `S`
```
namespace std {
	template<> 
	struct tuple_size<S> : std::integral_constant<size_t, 3> {}; // 3 Elements in s
}
```

Then we might do something like this: 
```
int main(){ 
	S s{42, 3.14, 'a'};


	constexpr std::size_t size = std::tuple_size<S>::value;

	std::cout << "The size of S is " << size << std::endl; // Outputs 3
}
```

Then we can specify `std::get`, which is Tuple specific, tuples are weird like this:
```
namespace std { 
	template<std::size_t N> // need to pass size_t in <>
	auto& get(S& s);

	template<>
	auto& get<0>(S& s) { return s.m0; }

	template<> 
	auto& get<1>(S& s) { return s.m1; }

	template<>
	auto& get<2>(S& s) { return s.m2; }
}
```


Back to tag dispatching: 
```
template<int i>
struct int_const{};


template<typename S> 
auto Struct2Tuple(S s, int_const<1> count)
{ 
	auto [m0] = s;
	return std::make_tuple(m0);
}

template<typename S> 
auto Struct2Tuple(S s, int_const<2> count)
{ 
	auto [m0, m1] = s;
	return std::make_tupel(m0, m1);
}
// More overloads for more members
```

So here `Struct2Tuple` here is overloaded dependent on the type that we are putting into it: 

### Overload Function with Return Type
Not possible to overload a function only based on its return type. 
With a tag-dispatch technique, we can pass the return type as the last parameter to be the tag: 
```
std::string r(int a, std::string dummy)
{ 
	return "String returned," + std::to_string(a);
}

int r(int a, int dummy) 
{ 
	return a;
}

int main() { 
	std::cout << r(1, std::string{});
	std::cout << r(1, int{});
}
```
[Read More About It](**https://iamsorush.com/posts/cpp-variadic-template/**)
