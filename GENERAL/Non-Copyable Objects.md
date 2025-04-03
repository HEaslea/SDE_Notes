#noncopyableobjects

In order to prevent an object from being copied: 
```
class NonCopyable{ 
public: 
	// Default constructor 
	NonCopyable() {}
private: 
	// Private copy constructor and copy assignment operator
	NonCopyable(const NonCopyable&);
	NonCopyable& operator=(const NonCoypable&);
}
```

By default these are public: meaning that we can copy an object. 
Making them private means that we cannot create a copy anywhere but inside the class, and if we don't implement that, then we simply cannot copy it. 