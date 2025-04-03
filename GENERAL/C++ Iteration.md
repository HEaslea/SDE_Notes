```
for(int i = 0; i < array_size; i++)

// Range Based For Loop
for(auto& element : container)
{ }

// iterator based
// or std::begin(container)
for(auto i = vec.begin(); it != vec.end(); ++it)
{ }

// while()

// does once before check
do { 
}while()

std::for_each(container.begin(), container.end(), [](const auto& element)
{ 
	std::cout << element << std::endl;
})



```




