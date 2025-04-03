The example given to start this off: 
```
template<typename T, template<typename, typename> ... Containers> 
void testContainers();
```

`Containers` being the template parameter pack. 

Simply, calling `testContainers<int, ...> ` forget the second parameter, this would just have `T` to be `int`. 

The second `template<typename, typename> ... Containers` is a little more complicated. 
This means that we can accept multiple template parameter classes. 
Here we are specifying that we can take in multiple classes that should take in 2 parameters each. 
This is usually the way for containers that will require a type and an allocator. 

Then they are packed into `Containers. 

it's use might look a little like this: 
Testing to see if we can instantiate containers using a given type: 
```
template <typename T< template<typename, typename> ... Containers> 
void testContainers()
{ 
	// function imp
	// instantiating using a fold expression
	(Containers<T, std::allocator<T>>(), ...); // using a fold expression
}
```
Then we call it with something like this: 
```
int main() 
{ 
	testContainers<int, std::vector, std::list, std::deque>(); // ok
	testContainers<std::string, std::vector, std::list>();
	// all of these containers will work if we have two template parameters for each

	// Error
	// testContainers<int, std::map>();
	// std::map takes more than two containers. 
}
```
we might look at something like this as the implementation: 
```
template<typename T, template<typename, typename> ... Containers> 
void testContainers ()
{
	(void)std::initializer_list<int> {(
		[]() { 
			Containers<T, std::allocator<T>> container;
			std::cout << "Container Type: " << typeid(Containers<T, std::allocator<T>>).name() << std::endl;
			return 0;
		}(), 0)... }
}
int main() 
{ 
	testContainers<in, std::vector, std::list, std::deque>();
}
```

The use of the initializer list and the lambda is so that each container is processed in order. 
We cast the list to void so that it just sort of deletes itself. 
The `(void)` cast in front of the `std::initializer_list<int>{...}` expression serves to **silence compiler warnings** about unused variables or return values. Specifically, it ensures that any side effects of the expression are preserved, but the resulting value is ignored.
 So here we are creating an initializer list of ints, that result from the lambda calls. 
 Each time we call the lambda, we create a container of the specified type, then we just return 0 into the list. 
 `initializer_list<int>(tempFuncReturnInt(), tempFuncReturnInt());` This is essentially what we are doing. 
`[]() { ... }()` is a lambda function that is immediately invoked. 
I've never seen this but the comma operator here `[]() { ... }(), 0` is to evaluate the left hand side expression, discard its result, and then evaluate its right hand side expression (0). 
eg. `int num = (24, 78, 85);`
We have to use parentheses here, as the assignment operator has a higher precedence than that of the comma. 
The added effect of this is that it evaluates the first operand, discards it, then the second, then the third etc. meaning that in the example above, we are just looking at the 0, despite the first one being evaluated . 
`(Containers<T, std::allocator<T>>(), 0)...` expands the parameter pack Containers, and applies the expression to each element. 
This means that our initializer list will be instantiated to 0. 
I mean, this is such a little loophole to something much larger, I think this is to be thought of as checking through an expression, and then just discarding that to give a known value, that is essentially meaningless. 
The `(void)` cast at the end is to explicitly indicate that the result of the expression is intentionally discarded. 
We are looking at the side effect of the expression, rather than the actual value. 


We can use `(void)` when we want to suppress unused variable warnings, casting it to `(void)` means that you're intentionally not using the result. 
`(void)printf("Hello World"); // discarding the return value` 

