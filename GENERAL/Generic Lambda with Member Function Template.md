Something like this: 
```
template <typename T> 
class Processor 
{ 
public: 
	template<typename U> 
	void process(U value)
	{ 
		std::cout << "Value " << value << " of type " << typeid(U).name() << std::endl;
	}
};
```

```
int main() 
{ 
	Process<int> intProcessor;  // the fact that this is int doesn't matter
	// due to the inner template
	auto genericLambda = [&intProcessor](auto value)
	{ 
		intProcessor.process(value);
	};

	// then 
	std::vector<int> intValues = {1, 2, 3};
	std::vector<double> doubleValues = {1.1, 2.2, 3.3};
	std::vector<std::string> stringValues = {"hello", "world"};
	
	for(const auto& val : intValues)
	{ 
		genericLambda(val); // U = int
	}
	
	for(const auto& val : doubleValues) ... 
	// calls process U = double
	for(const auto& val : stringValues) ... 
	// calls process U = std::string
}
```

