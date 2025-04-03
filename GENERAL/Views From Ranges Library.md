An elegant way to manipulate collections of data through **views**, lightweight objects that represent transformations or selections on ranges without owning the data. 

- **No Copying or Ownership**: A view works with existing ranges or containers. 
- **Lazy Evaluation**: operations are performed only when the elements are accessed (eg. iterated over, otherwise the operation is not performed).
- **Composable**: Views can be combined to create a complex operations in a readable and efficient manner. 

They can be applied to any range. 

**Pipeline Syntax**: Using the `|` to chain multiple transformations. 

```
auto numbers = std::vector{1, 2, 3, 4};
auto square = [](auto v) { return v * v; };
auto sq_view = std::views::transform(numbers, square);
for(auto s: sq_view)
{ 
	cout << s << endl;
}
// will output 1, 4, 9, 16
```

Another way of writing it is: 
```
std::vector numbers{1, 2, 3, 4};
auto sq_view = numbers | std::views::transform([](int n){ return n * n; });
for(auto i: sq_view)
{ 
	cout << i << endl;
}
```

```
std::vector<int> nums{10, 20, 30, 40, 50};
auto first_two = nums | std::views::take(2);
auto skip = nums | std::views::drop(2);

for(int n : first_two) std::cout << n << " "; // outputs 10 20
for(int n : skip) std::cout << n << " "; // outputs 30 40 50
```

```
std::vector<std::vector<int>> nested = {{1, 2}, {3, 4}, {5}};
auto flat_view = nested | std::views::join
for(int n : flat_view)
{ 
	cout << n << "  : "; // outputs 1 2 3 4 5
}
```
That one is pretty amazing. 
```
    std::vector<int> numbers = {1, 2, 3, 4, 5, 6};

    // Pipeline: filter, transform, and take
    auto view = numbers
              | std::views::filter([](int n) { return n % 2 == 0; })  // Keep evens
              | std::views::transform([](int n) { return n * n; })    // Square them
              | std::views::take(2);                                 // Take first two

    for (int n : view) {
        std::cout << n << " "; // Output: 4 16
    }
```

### Implementation Details
1. **Iterators** - They provide iterators that traverse the underlying range lazily. Filtering a range doesn't evaluate all elements upfront - only the accessed elements are evaluated. 
2. **Pipeline Syntax** - The `|` connects multiple views in a readable format. This is achieved through function composition. 
3. **Lifetime** - Views do not own the underlying range. The range must outlive the view in this sense. 

The pipeline thing is done through overloading the pipeline operator. 

#### Ranges
This is anything that satisfies this `std::ranges::range` concept (hence the idea of a constrained type): 
- It has a **begin iterator** and an **end iterator**
- These iterators can be used to traverse the sequence of elements (they are the right category). 
They replace the need to explicitly pass `begin()` and `end()`. 
```
std::vector<int> vec = {1, 4, 2};
std::ranges::sort(vec); // no need for begin and end
```

#### Views
Are therefore a lightweight rapper around a range. 
Unlike the owning ranges (eg. `std::vector`) views do not own their data, like `string_view`. 
They reference the existing range and lazily apply transformations or filters. 
They are composable as well. 

```
std::vector<int> vec = {1, 2, 3, 4, 5};
auto view = vec 
	| std::views::filter([](int n) { return n % 2 == 0;})
	| std::views::transform([](int n) { return n * 2;})
```

A `view` is essentially a class that implements the `range` interface while wrapping the underlying range. 

```
template <typename Range>
class MyView {
private:
    Range base_range; // The underlying range

public:
    MyView(Range range) : base_range(std::move(range)) {}

    // Define iterator access
    auto begin() { return std::begin(base_range); }
    auto end() { return std::end(base_range); }
};
```
```
template <typename Range, typename Func>
class TransformView {
private:
    Range base_range;
    Func transform_function;

public:
    TransformView(Range range, Func func)
        : base_range(std::move(range)), transform_function(std::move(func)) {}

    class iterator {
    private:
        typename Range::iterator current;
        Func transform_function;

    public:
        iterator(typename Range::iterator it, Func func)
            : current(it), transform_function(std::move(func)) {}

        auto operator*() { return transform_function(*current); }

        iterator& operator++() { ++current; return *this; }
        bool operator!=(const iterator& other) const { return current != other.current; }
    };

    auto begin() { return iterator(base_range.begin(), transform_function); }
    auto end() { return iterator(base_range.end(), transform_function); }
};
```
Views hold a view or a reference or copy of the range they operator on `base_range`. 

![[Pasted image 20241120050645.png]]