# Using `inline`

If a template function or a member function is specialized and defined in a header file that is included in multiple translation units (`.cpp`) files.
Here using `inline` is recommended. 
Otherwise we might run into **multiple definition** errors, as multiple translation units will redefine a function. 
Now why doesn't this happen for normal template functions? 
- **ODR Exemption for Templates**: This rule does not apply to template functions, in different translation units. Template functions are implicitly instantiated as needed by the compiler. When the same function would be instantiated, the compiler and the linker will handle it, often merging or discarding. 
- The compiler will ensure that only one type specific instantiation is emitted during the final linking process, even if multiple translation units include the same header containing the template

If we take 
```
template <typename T> 
void print(T val)
{ 
	std::cout << val << std::end;
}
```

The linker will ensure that there are no duplicate symbols. 

However: **for specialized member functions** of a template class eg. `Box<int>::display();`

**THE COMPILER WILL TREAT THESE MEMBER FUNCTIONS AS THOUGH THEY ARE REGULAR FUNCTIONS**. 

The compiler won't treat it like it can be instantiated many times. 
It's treated as a regular, non-template fujcntion. 
If you define this specialization in a header file, then use that header file in a bunch of `.cpp` files, then you will find that we are going to have lots of instances of that function cropping up, redefinitions everywhere, and multiple redefinition errors. 

`inline` is an amazing tool that allows the compiler to see that even if we have redefinitions in other translation units, don't worry, that's fine. 
Therefore if we specialize these member functions, within the header file, then we should be using inline. It allows the same function to be defined in multiple translation units without causing a conflict. 
```
template<typename T> 
class Box 
{ 
	void display()
	{ 
		std::cout << "GENERIC VERSION" << std::endl;
	}
};

template<>
inline void Box<int>::display() 
{ 
	std::cout << "FOR INT" << std::endl;
}
```

There is a need for inline when : 
- The specialized function is defined in a **header** file, that is going to be included in multiple translation units (to prevent multiple definition errors). 
There is **NO** need for inline when: 
- The specialization is defined in a single `.cpp` file, as it will only be linked once, no redefinitions. 
Tem