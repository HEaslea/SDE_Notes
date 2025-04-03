[Here is the video](https://www.youtube.com/watch?v=NTWSeQtHZ9M&t=1296s)

### DOD Composition
Data Orientated Design - cache friendly. 
Thinking about the position of the data before we actually get anything running. 

Entity is an aggregate of components. 
Components only store data, they are logic-less. 

Logic is handled using systems. 

![[Pasted image 20240909134454.png]]

![[Pasted image 20240909134528.png]]
Code To Follow:


![[Pasted image 20240909134604.png]]
Systems are like keys, that try to fit into the entities, if they do, then we know that system will work with that entity. 

```
using Entity = std::size_t;

constexpr std::size_t maxEntities{1000};

struct BonesComponent {...};
struct AIComponent {...};
struct SpriteComponent {...};


struct Manager 
{ 
	std::array<BonesComponent, maxEntities> bonesComponents;
	std::array<AIComponent, maxEntities> bonesComponents;
	std::array<SpriteComponent, maxEntities> bonesComponents;
};
```

This is a somewhat simplification of the system. 


### Imp Details

Component types are known at compile time
They are simple logic-less classes. 

Entities are to be lightweight objects that just have their id. 
They contain a bitset of their available components. 
They also store metadata for handles and memory reclamation. 

Systems are implicit -> signatures are sued instead. A signature is a set of required component types. 
A signature is a bitset, which entities are queried by. 



Then we **can** store components, like we have above, with one component per array. 
However, this is very wasteful of memory. 

![[Pasted image 20240909135322.png]]

Then there is the idea of the mega-array, where the entity's components are lined up, all the components for every entity is in one contiguous array of memory, therefore we get something like this: 
![[Pasted image 20240909135427.png]]CacheFriendly - Minimizes memory waste. 

Some issues arise when we look at adding memory to a specific area of the array, for instance, we want to add a component to a specific Entity, where in the array do we do that? 

Using a buttload of modern C++ features: 
- Variadic template metaprogramming, tuples, auto... 
-  << More Functional >> programming style where possible (apparently). 


### OOP Approach
How you might do it:
Here is a quick diagram for the OOP implementation: 
![[Pasted image 20240909140111.png]]

Here the manager has ownership of the entities: 
and the groups are just for convenience. 

![[Pasted image 20240909140152.png]]

![[Pasted image 20240909140503.png]]![[Pasted image 20240909140717.png]]Here we are using `unique_ptr` in order to get polymorphism. 

Then he uses a map in order to group gameobjects by their type: 
`std::map<std::size_t, std::vector<GameObject*>> grouped;`
`cleanup` will remove dead Entities. 
In order to use this map we havwe to have a key for it: 
which he has in this: 
```
template <typename T> 
auto idOf() const noexcept
{
	return typeid(T).hash_code();
}
```

[Hash_Code](https://en.cppreference.com/w/cpp/types/type_info/hash_code)

Some notes from `Hash_code`

[Reference_Wrapper](https://en.cppreference.com/w/cpp/utility/functional/reference_wrapper)

Allowing you to store references in a copyable and assignable object. 
Really useful when you want to store references in a standard container like a `std::vector` or a `std::pair`. 

in C++ you cannot directly copy references. 

```
int a = 10; 
int& ref = a; 
int& ref2 = ref;
// well here, you cannot reassign ref or ref2 to refere to another variable
```

There is no copying of references. 
However, using `std::reference_wrapper`. 
```
#include <functional> 
#include <vector> 

int a = 10, b = 20;
std::reference_wrapper<int> ref = a; 
std::reference_wrapper<inte ref2 = ref; // this is fine :)
```

```
std::vector<std::reference_wrapper<int>> vec = {ref, ref2};
```
```
int main() {
    int a = 10;
    int b = 20;
	
    int& aref = a;
    int& bref = b;

    // here the values are being copied into the vector
    // therefore this is not a vector of references, but just of ints
    std::vector<int> vec = {aref, bref};
	
    for(auto& i : vec)

        std::cout << i << std::endl;
		
    return 0;
}
```

```
int main() {

    int a = 10; 
    int b = 20;

    int& aref = a;
    int& bref = b;

    // here the values are being copied into the vector
    // therefore this is not a vector of references, but just of ints
    std::vector<int> vec = {aref, bref};

    for(auto& i : vec)
        std::cout << i << std::endl; // 10 20

    for(auto& i : vec)
        ++i;

    for(auto& i : vec)
        std::cout << i << std::endl; // 11 21

    std::cout << a << "   " << b << std::endl; // 10 20

    return 0;
}
```
You can see how these aren't references, these are just ints that have been copied in. 

Here's a cool example from the `reference_wrapper` on CPPref: 
```
void println(auto const rem, std::ranges::range auto const& v)
{ 
	for(std::cout << rem; auto const& e : )
		std::cout << e << ' ';
}
int main() 
{ 
	std::list<int> l(10);
	std::iota(l.begin(), l.end(), -4);

	// can't use shuffle on a list, as it requires random access, but can use it
	on a vector
	std::vector<std::reference_wrapper<int>> v(l.begin(), l.end());

	// in thise sense we can shuffle the list

	std::ranges::shuffle(v, std::mt19937{std::random_device{}()});

	println("Contents of the list: ", l);
	println("Contents of the list as seen through a shuffled vector", v);

	std::cout << "Doubling the values int he initial list... \n";
	std::ranges::for_each(l, [](int& i) { i *= 2;});

	println("Contents of the list, as seen through a shuffle vector : ", v);
}
```
Now the second argument type, looks like it uses two type declarations, however, it does only declare one, and this is because of Concepts C++20. 

Here we are specifying constraints on template parameters: 
`std::ranges::range` is a concept that checks if the type satisfies the requirements of a range. 
Ie. that it can be iterated over

`std::mt19937` is a random number generator known as Mersenne Twister pseudorandom number generator. 
Here it is being seeded with the random number generated by `std::random_device`. 

#### Resuming on
Then he does this thing with
```
template<typename T, typename ... Args>
auto& emplace(TArgs&&... mArgs)
{ 
	objects.emplace_back(std::make_unique<T>(FWD(mArgs)...));
	auto& result(*(objects.back()));

	result.manager = this;

	// placing into the map
	grupoed[idOf<T>()].emplace_back(&result);
}
```

Then the next logical step is to define game objects: 
`class PhysObject : public GameObject`
```
class PhysObject : public GameObject
{ 
private: 
	Vec2f pos, vel, acc; 
	
public:
	// update per change in frame time
	void update(FT mFT) override
	{ 
		vel += acc * mFT;
		pos += vel * mFT;
	}

	/... 
};
```

There is a sharing of data, creating more and more branches.

So then giving some thought to how we want to construct this whole thing: 

##### Elements of the Architecture
- **Component** - Simple logic-less data-containing struct -> cache friendly. 

- **Component-List** - Compile_time list of the component types.

- **Tag** - simple Logic_less, and data-less struct. 

- **TagList** - Compile-time list of tag types. 

- **Signature** - compile-time mixed list of required components and tags. 

- **SignatureList** - Compile time list of signature types: 


- **ENTITY** - Simple struct pointing to the correct component data. 
- Contains a bitset to keep track of available components
- and tags and a flag to check if the entity is alive or not


- **Manager** - "Context" class. will contain entities, components, signatures, metadata, Handling entity/component creation/removal, with entity iteration and much more. 

- **Handle**: Layer of indirection between the entities and the user. Handles sued to keep track and access an entityt. It will be possible to check if the entity is still valid through an existing handle. 



The idea that he is using is very "Modern C++", as he sets up a `Settings` struct, that holds policies that the user defines. 



