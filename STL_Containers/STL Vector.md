As mentioned in other notes: 
Dynamic growth, grows and shrinks as needed. 
Cache-friendly - contiguous memory usage. 
If there is frequent insertion and deletions in the middle, then vector is going to start struggling. 

Random Access. 

Dynamic Array replacement. 

Sequential Data Storage. 

Frequent back insertion - inserting in the middle is going to be bad, as things have to move around, if order doesn't matter, then just put at the back. 

Very low overhead in data - just the elements next to each other, not like linked lists. 

Cache friendly iteration. 

Good portability and compactness. 

Polymorphic : if storing data pointers to objects of polymorphic classes. 

#### Performance
Insertion: `O(1)` average-case at the end, `O(n)` for elsewhere

Deletion: `O(1)` at the end, `O(n)` for the middle, as per insertion. 

Access : `O(1)` in any position - due to random access, index offset. 

If we have to grow, then we have to copy everything. 

Auto memory management. 
Typical doubling of size when capacity is reached. 
Allocators can influence this behaviour, permitting more fine-grained control. 

Threads safe, however, there needs to be some external synchronization. General `mutex` will do. 

`std::list` - if we wanted to optimise middle insertions. 

`std::sort` `O(n log n)` with `std::sort()`

`std::binary_search()` `O(log n)`. 

`emplace_back` : constructs elements directly

`resize` : changes the number of elements

`shrink_to_fit` reduces memory usage

Some points: 
- Pre-allocating memory with reserve
- Using `emplace_back` for efficient insertion
- Iterators for traversal and modification
- `std::remove`


```
#include <algorithm> 
#include <iostream> 
#include <vector> 

class Employee{ 
public: 
	Employee(int _id, const std::string& _name)
	 : id(_id), name(_name) {}

	int getId() const { return id; }

	const std::string& getName() const { return name; }

	void setName(const std::string& newName)
	{ 
		name = newName;
	}
	
private:
	 int id{0};
	 std::string name;
};

int main() {
	std::vector<Employee> employees;
	
	employees.reserve(5);
	employees.emplace_back(1, "Lisa");
	employees.emplace_back(2, "Corbin");
	employees.emplace_back(3, "Aaron");
	employees.emplace_back(4, "Amanda");
	employees.emplace_back(5, "Regan");
	
	for (const auto& emp : employees)
	{ 
		std::cout << "ID: " << emp.getId() << ", Name:" << emp.getName() << "\n";
	}

	

}
```

