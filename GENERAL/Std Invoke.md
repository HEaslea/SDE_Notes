Is a generic and generalized way to call functions.  It is the generic interface for calling callables. 

It is used when we have `<algorithm>, <ranges>`. 

Here's an interesting one, as it can be used with pointers to data members/functions as well. 

When we provide a pointer to member and an instance of the class, `std::invoke` dereferences the pointer and extracts the member value. 

`std::invoke(&Person::age, person_instance)`

```
struct Person
{ 
	std::string name;
	int age;
};

int main() 
{ 
	Person p{"Alice", 30};

	// access the "age" member
	int age = std::invoke(&Person::age, p);

	std::cout << "AGE: " << age << std::endl; // will output 30
}
```

Modifying the age: 
```
Person p{"Bob", 25};

std::invoke(&Person::age, p) = 35; 
```

Calling a member function
```
Person p{"Charlie", 40};
std::invoke(&Person::greet, p); // call greet for this person
```

Using this idea with projection: 
```
std::vector<Person> people = {{"Alice", 30}, {"Bob", 25}, {"Charlie", 35}};

std::ranges::sort(people, {}, &Person::age); // getting the age of each person 
// the {} here is for the default comparator using operator< here

// this is the equiv
std::sort(people.begin(), people.end(), [](const Person& a, const Perons& b)
{ 
	return std::invoke(&Person::age, a) << std::invoke(&Person::age, b);
});
```

```
// Base Case, when there's only one element, apply the function to it
template <typename Func, typename T> 
void for_each_tuple_impl(Func&& func, T&& value)
{ 
	std::forward<Func>(func)(std::forward<T>(value));
}

template <typename Func, typename Head, typename ... Tail> 
void for_each_tuple_impl(Func&& func, Head&& head, Tail&& ... tail)
{ 
	std::foward<Func>(func)(std::forward<Head>(head)); // Apply to the head
	for_each_tuple_impl(std::forward<Func>(func), std::foward<Tail>(tail)...); // recurse for this
}

// main function, the wrapper that will start the recursion off
template <typename Func, typename ... Args> 
void for_each_tuple(Func&& func, Args&& ... args)
{ 
	for_each_tuple_impl(std::foward<Func>(func), std::foward<Args>(args)...);
}
```

Using recursion to go over tuples. 

```
template <typename Func, typename Tuple, std::size_t ... I>
void for_each_tuple_impl(Func&& func, Tuple&& t, std::index_sequcne<I...>)
{ 
	(func(std::get<I>(std::foward<Tuple>(t))), ...);
}

template <typename Func, typename ... Args> 
void for_each_tuple(Func&& func, std::tuple<Args...>& t)
{ 
	for_each_tuple_impl(std::foward<Func>(func), t, std::index_sequence_for<Args...>{}):
}
```

```
// Base class for empty tuple
template <typename ... Types> 
class Tuple;  

template <> // no parameters here
class Tuple<> {}; // empty

template <typename Head, typename ... Tail> 
class Tuple<Head, Tail...> : private Tuple<Tail...> 
{ 
private: 
	Head value;
public: 
	Tuple(Head head, Tail ... Tail)
		: Tuple<Tail...>(std::forward<Tail>(tail)...), value(std::forward<Head>(had)) {}

	Head& getHead() { return value; }
	const Head& getHead() const { return value; }

	Tuple<Tail...>& getTail() { return * this; }
	const Tuple<Tail...>& getTail() const { return * this;}
}
```

