Sort a vector 
Search for elements 
Manip vectors
Custom comparators and predicates

### Sorting a Vector
`std::sort` uses a mixture of things : Introsort (starting with Quick Sort (best average-case performance)). Then might swap to HeapSort if depth exceeds a threshold. 
As QuickSort is worst case `O(n^2)1` when array is basically already sorted and the pivot is taken from the front or the back. 

Insertion sort when the array is really small. 
```
std::vector<int> numbers {...};

std::sort(std::begin(numbers), std::end(numbers));
```

#### Introsort - Introspection sort 
Makes a decision based on the what it gets. 
Hybrid and adapts to certain scenarios. 
quicksort, heapsort and insertion sort. 
Depending on the case that we are given. 


Descending Order : `std::sort(n.begin(), n.end(), std::greater<>());`

Send in a default constructed `greater` that is a functor.

### Sorting Custom Data Types

```
struct Person 
{ 
	std::string name;
	int age{0};
	
	Person(std::string n, int a) : name(n), age(a) {}

	friend std::ostream &operator<<(std::ostream &os, const Person & p)
	{ 
		os << p.name << " ( " << p.age << " )";
		return os;
	}
};

int main() 
{ 
	std::vector<Person> people = {Person("Regan", 30),
	Person("Lisa", 40), 
	Person("Corbin", 45) 
	};

	auto compareByName = [](const Person& a, const Person& b)
	{ 
		return a.name < b.name;
	};

	std::sort(people.begin(), people.end(), compareByName);

	// or
	std::sort(people.begin(), people.end(), 
	[](const Person& a, const Person& b) 
	{ 
		return a.age < b.age;
	});
}
```

## Strict Weak Ordering
Strict : when we have `comp(a, b)` and `comp(b, a)` cannot be both recorded as true. If `a < b` then `b < a` cannot be true. 

Weakness : two different elements cannot be equivalent. weak would be allowing of equivalence of class. 
strict weak - two different elements can be considered equivalent. Two people of the same age - would be in the same equivalence class. 

Transitivity: if `a < b` and `b < c` then `a < c`. Similar to equivalence of a == b then b == c, then a == c. 

Invalidating it looks like this: 
```
auto badComp = [](int a, int b) { return a <= b; };
```
This returns true when two numbers are equal. 

### Searching
Linear with `std::find`. 
Gives a pointer to the location, will point to `n.end()` if not found.

Binary with `std::lower_bound` and `std::upper_bound`. 

Can use these to find where the next best bit would be to put something: 
Given `1 3 3 5 7`

`std::lower_bound` for 3 in this would give : 1
`std::lower_bound` for 4 in this would give : 3
`std::upper_bound` for 3 in this would give : 3

Binary - quick with a condnition

Linear Search - always works no matter the condition. 

##### Transforms
Copying 
```
std::vector<int> source = {1, 2, 3, 4, 5};
std::vector<int> destination(5);

std::copy(source.begin(), source.end(), destination.begin());
```

Rotating: 
```
std::vector<int> values = {1, 2, 3, 4, 5};
std::rotate(values.begin(), values.begin() + 2, values.end());
```
this will give `3, 4, 5, 1, 2`

Filling a vector with `std::fill`

###### Using Manipulators
imagine that we have a list of songs, then we just need to sort all the songs to what the user wants. 
Then you could use `rotate` to put new music into the mix. 
`std::rotate(begin, middle, end)`
`middle` being the new first element after rotating. 

If we are not sure about the size of the destination - then we need to use `std::back_inserter`. 

`std::rotate` minimizes moves of elements. 

###### `std::rotate` Implementation
```
template <typename Iter> 
void my_rotate(Iter being, Iter middle, Iter end)
{ 
	std::reverse(begin, middle);
	std::reverse(middle, end);
	std::reverse(begin, end);
}
```
Given `1, 2, 3, 4, 5`
Then we do `rotate(begin, to 3, end)`
Reverse begin to middle `2, 1, 3, 4, 5`
Reverse middle to end `2, 1, 5, 4, 3`
Reverse the whole thing `3, 4, 5, 1, 2`

This means that it will be `O(n)`. 

Avoiding reverse looks something like this: 
```
template <typename Iter> 
void my_rotate_swap(Iter begin, Iter middle, Iter end)
{ 
	Iter next = middle; 
	while(begin != next)
	{ 
		std::swap(*begin++, *next++);
		if(next == end) next = middle; // wrap around
		else if(begin == middle) middle = next;
	}
}
```
Works well for small rotations `O(n)` as well. 


### A Predicate 
A predicate is a callable object that returns a `bool`. 

```
std::vector<int> x = {1, 2, 3, 4, 5};
int evens = std::count_if(x.begin(), x.end() [](int n)
{ 
	return n % 2 == 0;
})
```

Statelessness - should not be changing state, no side effects or change behaviour between calls. 
Avoiding unecessary computations as theyw ill be called a lot. 

##### Defining a Struct and Class
For more defined behaviour. 
```
struct Student 
{ 
	std::string name;
	int grade;
};

std::vector<Student> students = { ... };

std::sort(students.begin(), students.end(), [](const Student& a, const Student& b { 
	if(a.grade == b.grade) { return (a.name < b.name);
	return (a.grade > b.grade);}

}));
```

Then using a `struct` with a `()` operator: 
```
struct SortByGradeThenName
{ 
	bool operator() (const Student& first, 
		const Studen& second) const 
		{ 
			if(first.grade == second.grade)
			{ 
				return (first.name < second.name);
			}
			return (first.grade > second.grade);
		}
		// alphabetical, but highest grade first
}
```


#### Container Invariants and Iterator Invalidation
Vector - elements stored contiguously in memory.

Iterator invalidation - changed data at where we are pointing or the data doesn't exist there anymore. 
With vector, say that we are pointing to a place, then we add another element to the vector, it all moves, then the iterator will no longer be valid right. 
When the vector's layout, our iterators may be invalidated. 

```
std::vector<int> numbers = {1, 2, 3, 4, 5} ;


std::vecdtor<int>::iterator it = numbers.begin() + 2;

for(int i = 6; i <= 1000; i++) 
	numbers.push_back(i);

// that it iterator will be invalidated now

it = numbers.begin() + 2; 
numbers.insert(it, 99);

it = numbers.begin() + 3; 
numbers.erase(it);

return 0;
```

`insert` and `erase` can invalidate an error. 

The basics that is that we need to re-acquire an iterator whenever we believe that it could be invalidated. 
`insert` - if we exceed current capacity. 
`erase` - removing from the middle - meaning that all the elements after that one will shift. 

mostly just the idea that an iterator will be invalidated if anything moves due to reallocation. 

### Counteract Invalidation
`reserve` in advance if we have a rough idea of how many elements that we might have. 
This can reduce reallocation. 

Consider storing positions and not iterators - they will always be valid and help us understand better where we are in an array. 

After a disruptive action, just re-acquire the iterator. 

`<algorithm>` they handle potential invalidations internally, safeguarding for us. 

Using predicates and custom comparators - make sure that we don't change the vector. 

So `reserve` early, use indexes alongside, refresh iterators after disruptive operation, use `<algorithm>` that are already optimized, using read only operations (in comparators and predicates). 

#### Dealing with Invalidation/Multi-threaded Scenarios
- `mutexes` and `locks` - only one thread to change the vector at any one point. 
- `atomic` operations - completed without interruption . 
- `thread-safe` containers - they are designed to handle this. 

```
std::mutex vecMutex; 

void add_to_vector(std::vector<int> & numbers, int value)
{ 
	std::lock_buard<std::mutex> guard(vecMutex); // will acquire and release automatically (RAII)
	numbers.puhs_back(value);
}

void print_vector(const std::vector<int> & numbers)
{ 
	std::lock_guard<std::mutex> guard(vecMutex);

	for(int num : numbers) { std::cout << num << " : ";}
}

int main() 
{ 
	std::vector<int> numbers;

	// remember that we are using std::ref here to pass by reference
	std::thread t1(add_to_vector, std::ref(numbers), 1);
	std::thread t2(add_to_vector, std::ref(numbers), 2);

	t1.join();
	t2.join();
	
	std::thread t3(add_to_vector, std::ref(numbers));

	t3.join()
}
```
Remember that `std::ref` will create a `std::reference_wrapper<T>`. 

Remembering that mutexes can cause deadlocks. 

Thread-safe containers - or - advanced synchronization techniques. 

