From Meyer's Effective Modern C++. 

IDE's obviously show you when you hover over. 

### Compiler Diagnostics
Having a class template that we don't define, will produce an error. 

```
template <typename T> 
class TD;

int main() 
{ 
	TD<decltype(x)> xType; // here wewill get an error
	// this is because this is an undefined type
	// there is no class definition here
}
```

```
C:/Users/hugoe/OneDrive/Desktop/Leetcode/main.cpp: In function 'int main()':
C:/Users/hugoe/OneDrive/Desktop/Leetcode/main.cpp:117:21: error: aggregate 'TD<int> xType' has incomplete type and cannot be defined
  117 |     TD<decltype(x)> xType;
      |                     ^~~~~
ninja: build stopped: subcommand failed.
```

However, having to compile just for this is silly. 

### Run Time Viewing
Using `typeid(var).name()` in order to get a valid output for a reference to a `std::type_info` object. 

The compiler generates one `type_info` object per type, and `typeid()` gets the reference for it. 

Microsoft compilers will give us a less mangled type. 

