### Shallow Copy
Where we copy only the pointer or the reference to the underlying data, rather than duplicating the data itself. Copy On Write is a cool idea, where we copy the pointer/reference to the underlying data, and then when we write that data, through one of the references, that's when we copy, essentially a sense of lazy initialization. 

```
class Shallow
{ 
public: 
	int* data;
	Shallow(int val)
	{ 
		data = new int(val);
	}

	// default copy constructor will create a shallow copy
};
```

### Deep Copy
Involving copying the actual data, changing the data of an object will not affect any of the others. 
Every object has its own independent data. 
There is no sharing of resources. 
This will be more expensive, and for all the elements, the copy constructor will be called for that element and T. 

```
class Deep
{ 
public: 
	int* data;
	Deep(int val)
	{ 
		data = new int(val);
	}

	Deep(Deep const& other)
	{ 
		data = new int(*other.data); // Allocating new memory
		// therefore a deep copy
	}

	// There is RAII here, as we have ownership 
	~Deep()
	{ 
		delete data; 
	}
};
```

### Key Differences: Shallow Copy vs. Deep Copy

|Aspect|Shallow Copy|Deep Copy|
|---|---|---|
|**What is copied?**|Pointers/references to the same underlying data|A new copy of the actual data (independent)|
|**Memory allocation**|No new memory for data, just pointers/references|New memory is allocated for the copied data|
|**Effect of changes**|Changes to the data are reflected in both objects|Changes to one object don’t affect the other|
|**Efficiency**|Faster and uses less memory (no duplication of data)|Slower and uses more memory (due to duplication)|
|**Ownership**|Shared ownership of data|Each object has its own copy of the data|
|**Risk**|Can lead to unintended side effects or double frees|Safer, but more resource-intensive|