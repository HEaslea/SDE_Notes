STL is a great lib, for algorithms etc., however when we have to pass in a beginning and and end, it gets long eg. 
```
std::vector<int> numbers = {1, 2, 3, 4, 5, 6, 7, 8};

std::vector<int> evenNumbers; 
std::copy_if(begin(numbers), end(numbers), std::back_inserter(evenNumbers), 
	[](int n){ return n % 2 == 0; });

std::vector<int> results;
std::transform(begin(evenNumbers), end(evenNumbers), std::back_inserter(results)
	[](int n){ return n * 2; });
```

It's a lot of code, that doesn't really do a whole lot :(. 

Then, with some inclusions: 
```
#include <range/v3/view/filter.hpp>

// we can do something like this

auto evenNumbers = numbers | ranges::view::filter([](int n){ return n % 2 == 0;})
						   | ranges::view::filter([](int n) { return n * 2;});

for(int n : results)
{ 
	std::cout << n << " ";
}
```



Then we had the `std::ranges`;
[Ranges CPPRef](https://en.cppreference.com/w/cpp/ranges)

The example from the site: 
```
#include <iostream>
#include <ranges>

int main() 
{ 
	auto const ints = {0, 1, 2, 3, 4, 5};
	auto even = [](int i){ return 0 == i % 2;};
	auto square = [](int i){return i * i;};
	
	for(int i : int | std::views::filter(even)
					| std::view::filter(square))
		std::cout << i << "  ";

	// or
	for(int i : std::views::transform(std::views::filter(ints, even), sqaure))
		std::cout << i << "  ";
}

