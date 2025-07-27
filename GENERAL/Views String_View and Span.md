### Views
Small objects, that are meant to be copied. 

They guarantee nothing when it comes to memory that they point to. 

They are non-owning reference types. 

However, they are still very closely related to that container to which they point to. 

#### `string_view`
[CPPReference of Stringview](https://en.cppreference.com/w/cpp/string/basic_string_view.html)
They typically point to some memory that is owned by some `std::string`. 
Could also point to some string literal. 

The core parts of `std::string_view`: 
```
const char* data; 
size_t size; 
```

It's key to remember that `std::string` owns the memory, the `std::string_view` in this sense, is non owning. 

There is a reference here that they can have `/0`, meaning, that they are not null terminated. 

However, `std::string` does have to be null terminated. 

Something important is the idea that using `substr()` with `string_view` is constant time, however, `substr()` using `string` will be linear time. 
This distinction is very important if we just want to point to some characters, rather than changing slices of the original string. 

```
auto some_func(std::string_view)
{ 
	// Process the string
}

some_func("Hello there"); // fine to pass a string literal here. 
```

Here we bypass the need to build a `std::string`. 

