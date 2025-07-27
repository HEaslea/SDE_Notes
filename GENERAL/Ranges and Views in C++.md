### Why Ranges? 
C++ 20 introduced some new features: 
- Concepts that will define requirements on iterates and ranges, can now be better checked by the compiler and provide more help during development. 
- New overloads of all functions in the `<algorithm>` header are constrained with the just mentioned concepts and accept ranges as arguments rather than iterator pairs. If ranges can be taken as arguments, then surely we should start using them. 
- Constrained iterators in the iterator header
- Range views, make it possible to compose algorithms.

##### Limitations on the Algorithm Lib
They lack composability. As in, we should be able to build them up like linux command with `|`
Say we have: 
```
struct Student { 
	int year_{};
	int score_{};
	std::string name_{};
	//... 
};
```

Say that we want to find the highest score from a big collection of students in their second year, we would use `max_element()` on `score_`, but since we only want secondary year students, it's trickier, and we have to go through a few different adjustments. 

```
auto get_max_score(const std::vector<Student>& students, int year)
{ 
	// get everything by value, including the args
	auto by_year = [=](const auto& s) { return s.year_ == year; };
	auto v = std::vector<Student>{};
	// getting a separate vector of students found in this year
	std::ranges::copy_if(students, std::back_inserter(v), by_year);
	auto it = std::ranges::max_element(v, std::less{}, &Studen::score_);
	return it != v.end() ? it->score_ : 0; // just to check that we have a student with the max score, if not, just return 0
}
```

This is easy enough to understand, however, we are making a copy, with `auto v = std::vector<Student>{};` and then we are copying into it, depending on the year that we are given, through the lambda `by_year`. 

We could use a `for` loop, however, this defeats the idea of composability of algorithms. 

```
auto get_max_score(const std::vector<Student>& students, int year)
{ 
	auto max_score = 0; 
	for (const auto& student : students)
	{ 
		if(student.year_ == year)
			max_score = std::max(max_score, student.score_);
	}
	return max_score;
}
```

There's the idea here that we have lazy evaluated ranges. 
There is no work done until we absolutely need it. 

The previous example if written using views from the `Ranges` library. 

```
auto max_value(auto&& range)
{ 
	const auto it = std::ranges::max_element(range): 
	return it != range.end() ? *it : 0;
}

auto get_max_score(const std::vector<Student>& students, int year)
{ 
	const auto by_year = [=](auto&& s) { return s.year_ == year; };
	return max_value(students
	 | std::views::filter(by_year)
	 | std::views::transform(&Student::score_));
}
```

Notice, there is no copying and creating of empty vectors that we copy into

Quick read through: 
We have the lambda `const auto by_year = [=](auto&& s) { return s.year_ == year; };`
This `[=]` will capture the arguments as well. 

So then `return max_value` we are returning the `max_value`, that is the helper function that we wrote, that just uses the `std::ranges::max_element` that just takes in the range produced from the pipeline of `students ....`. 

Technically ranges are just an abstract over the top of two iterators. 

```
auto numbers = std::vector{1, 2, 3, 4};

auto square = [](auto v) { v * v ; };

auto squared_view = std::views::transform(numbers, square);

// then we are going to iterate over them
for(auto s : squared_view) 
{ 
	// output
}
```

Please note, that whenever we write: 
```
for (int n : numbers) // n
```

Similar to Python actually: 
```
auto&& __range = numbers; 
auto __begin = std::begin(__range);
auto __end = stdd::end(__range);

for(: __begin != ___end; ++__begin)
{ 
	int n = *__begin;
}
```

However, python will use generator functions

```
for n in numbers: 

_iterator = iter(numbers)
while True: 
	try: 
		n = next(_iterator)
		# do something with n
	except StopIteration
		break
```

The `view` is to be lazy evaluated. 

`views` and  `ranges`, at least for now in C++ 20, are primarily abstractions over iterators that let you compose operations (like filter, transform, take eqc) without copying any data, like we saw earlier on. 

Take this idea with you, that if there is a way of working with something, and we notice it uses more memory, we can lazy evaluate too, meaning that we are not engaging in calculations, when we are not using the results. 

`std::ranges` - anything that we can iterate over, as this is a general interface over iterables. 

`std::views` - lazy, composable wrappers over iterators.

It's key to remember, that we are not copying any data, nor do the ranges and the views own the data here. 

We can materialize this to a container using `std::ranges::copy()`. 

We can create a filtered view, where only part of the range is visible, remember that we are not creating a new container here. 

```
auto v = std::vector{4, 5, 6, 7, 6, 5, 4};

auto odd_view =  //  I believe this is a view here
	std::views::filter(v, [](auto i) { return (i % 2) == 1; });
```

[C++Ref on Views::Filter](https://en.cppreference.com/w/cpp/ranges/filter_view.html)

```
auto even = [](int i) { return 0 == i % 2; };
auto square = [](int i) { return i * i; };

for(int i : std::views::iota(0, 6)
			| std::views::filter(even)
			| std::views::transform(square))
	std::cout << i << " ";
std::cout << "\n";
```

You can even flatten, a `vector` of `vectors`. 
```
auto lists_of_lists = std::vector<std::vector<int>> { 
	{1, 2},
	{3, 4, 5}, 
	{5}, 
	{4, 3, 2, 1}
};

auto flattened_view = std::views::join(list_of_lists);

// then just iterate over that flattened view

auto max_value = *std::ranges::max_element(flattened_view); // just care, as we get an iterator back
```

## Composable
