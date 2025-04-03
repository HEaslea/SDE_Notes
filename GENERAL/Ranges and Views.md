```
auto max_value(auto&& range)
{ 
	const auto it = std::ranges::max_element(range);
	return it != range.end()? *it : 0;
}

auto get_max_score(const std::vector<Student>& students, int year)
{ 
	const auto by_year = [=](auto&& s) { return s.year_ == year; };
	return max_values(students 
		| std::views::filter(by_year)
		| std::views::transform(&Student::score_));
}
```

Here we are not unnecessarily copying anything, which might have to be the case if we were not to use ranges and constrained algorithms such as this. 

### What are Ranges
#ranges 
They are an abstraction introduced in C++20, to make working with sequences of elements (arrays, vectors, etc.) easier, more expressive and safer. 
They extend the capabilities of STL Algs and Iterators. 

They read more naturally. 
They use lazy evaluation (like a proxy almost), they are only preformed, when they are absolutely necessary. 
They do not require lots of copies. 

Ranges use concepts, providing compile-time safety and meaningful error messages. 

Composable operations using the pipe operator. 

Another example: 

```
auto numbers = std::vector{1, 2, 3, 4};
auto square = [](auto v){ return v * v;};

auto squared_view = std::views::transform(numbers, square);

for(auto s : squared_view)
{ 
	std::cout << s << "  ";
}
```


