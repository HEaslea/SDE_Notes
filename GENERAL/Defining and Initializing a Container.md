#defininginitializingcontainers
[[Overview of Sequential Containers]]

Every container type defines a default constructor. Except `array`, the default constructor creates an empty container of the specified type. 
(not `array`), but other containers will take arguments that specify the size of the container, and initial values for the elements. 

### Initializing a Container as a Copy of Another Container
There are two ways to create a new container as a copy of another one; We can directly copy the container, or (excepting `array`) we can copy a range of elements denotes by a pair of iterators. 

To create a copy of another container, the container and element types must match, duh. 
We when we pass iterators, there is no requirement that the container types are identical. 
The element types can differ, as long as it's possible to convert, the elements we're copying to the element type of the container we are initializing: 

```
// each container has three elements initilaized from the given initializers
list<string> authors = {"Milton", "Shakespeare", "Austen"};
vector<const char*> articles = {"a", "an", "the"};
list<string> list2(authors); // ok: types match
deque<string> authList(authors); // error; container types don't match
vector<string> words(articles); // error, element types must match too 
//ok converts const char* elements to string, which is fine
forward_list<string> words(articles.begin(), articles.end());
```
Initializing using a container as a copy of another, the container type and element type should both be identical (or castable to). 
The constructor takes two iterators, uses them to denote a range of elements that we want to copy. 
The iterators mark the first and one past the last element to be copied. 
The new container has the same size as the number of elements in the range. 
Each element in the new container is initialized by the value of the corresponding element in the range. 

As we are copying a range, which is a super nice way of doing it, we copy up to but no including the "last" iterator. 
```
// copies up to but not including the element denoted by it
deque<string> authList(authors.begin(), it);
```

### List Initialization
Under new standard we can list initialize a container: 
```
// each container has three elements initialized from the given initializers
list<string> authors = {"Milton", "Shakespeare", "Auste"};
vector<const char*> articles = {"a", "an", "the"};
```
We explicitly specify values for each element in the container. 
For types other than `array`, the initializer list also implicitly specifies the size of the container. 

### Size Related Constructors
Associative containers are ones that have a key and a value.
We can create a size for the containers from the get go. 
```
vector<int> ivec(10,-1);
list<string. svec(10, "hi!"); // ten strings, each element is "hi!"
foward_list<int> ivec(10); // ten elements, initialized to 0
deque<string>svec(10); // ten elements, each an empty string
```

If the element type does not have a default constructor, then we must specify and explicitly element initializer along with the size. 
### Library `arrays` Have a Fixed Size
The size of a library array is also part of its type. 
There when we create one, we must specify the size too. 
```
array<int, 42> // type is : array that holds 42 ints
array<string, 10> 
```

The fixed-size nature of the arrays also affects the behaviour of the constructors that array does define. 
A default-constructed array is not empty, it has as many elements as it does size. 
They are all default initialized, just as are elements in built-in arrays. 

```
class TestSub{
public:
    TestSub() { cout << "TestSub init : ";}

private:
    int year;
};

class TestElement{
public:
    TestElement() { cout << "TestELement init" << endl;}

private:
    TestSub sub;
};

int main(){
    array<TestElement, 10> newarr;
```
Look at how they are constructed: 
![[Pasted image 20240315174134.png]]

IF we list initialize an array, then the list must be = or < than the size of the array. 
If there are fewer list elements than the size of the array, then the remaining elements will be default initialized. 

```
array<int, 10> ival; // ten default-initialized ints
array<int, 10> ia2 = {0,1,2,3,4,5,6,7,8,9}; // all are list initialized
array<int , 10> ia3 = {42}; // ia3[0] is 42, remaining elements are 0
```

There is no restriction on array for assigning or copying. 
```
int digs[10] = {0,1,2,3,4,5,6,7,8,9};
int cpy[10] = digs; // error no copy or assignment for built-in arrays

// however, with our library array
array<int, 10> digits = {0,1,2,3,4,5,6,7,8,9};
array<int, 10> copy = digits; // ok so long as array types match
```

