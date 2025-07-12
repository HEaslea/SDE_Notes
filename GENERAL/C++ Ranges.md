Typically referring to a sequence of elements that can be iterated over. 

A **range** is an abstraction that represents a sequence of values along with operations that can be performed on that sequence. 
Similar to a combination of an iterator pair (begin and end) or a container, however, it will offer a more expressive way to work with data. 

There is the : 
1. **begin** Iterator
2. **endl** Iterator

### C++20 Ranges Library
`<ranges>`
1. **Range Views** : operations that transform or filter a range without modifying the underlying sequence (eg. `std::views::filter, std::views::transform`). 
2. **Range Adaptors** : chaining multiple operations together, creating pipelines (`range | std::views::filter(predicate) | std::views::transform(func)`). 
3. **Range Algorithms** : Variants of standard algorithms that can work directly with ranges (`std::ranges::sort, std::ranges::find`). 


##### Simple Range Usage
```
#include <ranges> 
#include <vectors> 

std::vector numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9};
auto even_doubles = numbers | std::views::filter([](int n){ return n % 2 == 0;})
							| std::views::transform([](int n) { return n * 2});

// we have filtered out all the odd numbers

for(int n : even_doubles)
{ 
	cout << n << " "; // output 4 8 12 16
}
```
 These are pretty amazing things: 
 Without using them we end up with this salad of words: 
 ```
std::vector<int> numbers = {1, 2, 3, 4, 5...};
std::vector<int> evens; 
for(int n : numbers)
{
	if(n % 2 == 0)
		evens.push_back(n * 2);
} 
```
Having looked at it, might not be that much better or readable. 


