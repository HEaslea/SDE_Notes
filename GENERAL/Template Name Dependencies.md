A **dependent name** is obviously one that depends on the template parameter. 
The actual meaning of the name isn't known until the template is instantiated with specific template arguments. 

```
template <typename T> 
class MyClass
{ 
public: 
	typename T::value_type member; // value_type is a dependent name here
};
```
Here it depends on what `T` is when the template is instantiated. 

Something such as: 
```
template <typename T> 
void func() 
{ 
	int x = 5; // x is a non dependent name
}
```

#### Two Phase Name Lookup
1. First Phase (Template Definition) : Compiler checks names that are independent of template parameters. It doesn't resolve dependent names yet. 
2. Second phase (Template Instantiation) : when instantiated with specific arguments, the compiler resolves dependent names based on the actual types. 

Therefore if a name is dependent, then providing additional help to the compiler in order to understand how to interpret it. 

`typename T::value_type member;` Without the `typename` here, there is ambiguity as `T::value_type` could be a static member variable. 

```
template <typename T> 
class MyClass 
{ 
public: 
	void func() 
	{ 
		typename T::template inner_template<int> obj; 
	}
};
```
Here `template` is used so that the `<` and `>` are not used as less than or more than operators. 



#### Dependent Base Classes
```
template <typename T> 
class Derived : public T 
{ 
public: 
	void func() 
	{ 
		// accessing a member from T, using this->
		this->memberFunction(); 

		// this one is more clear
		T::memberFunction();
	}
};
```

```
template <typename T> 
auto getMemberType() -> typename T::member_type 
{ 
	return typename T::member_type(); // returning default init of this type
}
```
