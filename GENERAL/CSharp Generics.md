What is C++ templates, Alex?
## Introducing C# Generics

```
public class MyClass<T>
{ 
	public T Data { get; private set; } // will set up a _data

	public MyClass(T value)
	{ 
		Data = value;
	}

	public T GetData()
	{ 
		return Data;	
	}
}

MyClass<int> intObj = new MyClass<int>(10);
```

There is already concepts and constraints in here: 
```
public class Repository where T : IEntity, new() 
{ 
	public T Create()
	{ 
		return new T();
	}
}
```

Similar to C++20 Concepts
```
#include <concepts> 
template <typename T> 
concept IEntity = requires(T a) {
	{ a.id } -> std::convertible_to<int>;
};

template <IEntity T> 
class Repository
{ 
public: 
	T create() 
	{ 
		return T();
	}
};
```


##### Specializations
Well this is one of the cooler things about C++ templates. 
You would achieve similar affects: 
- Inheritance
- Interfaces
- In Class Type checks - which is similar to if constexpr

```
public class MyClass<T>
{ 
	public void Display()
	{ 
		if(typeof(T) == typeof(int))
		{ 
			Console.WriteLine("Integer Type");
		}
		else 
		{ 
			Console.WriteLine("Generic Type");
		}
	}
}
```

