#singletone
##### Restricting A Class To One Instance
Various files, might use the same file wrapper system. 

**Providing a Global Point of Access**: 
The singleton will create the single instance. 
Providing the globally available method in order to get it. 

Here the static `instance_` holding the instance of the class, and the public static `instance()` will grant access to `instance_`. 

```
class FileSystem{ 
public: 
	static FileSystem& instance()
	{ 
		static FileSystem* instance = new FileSystem(); 
		return *instance; 
	}
	
private: 
	FileSystem() {}
};
```

The `static FileSystem& instance()` :  Providing access to the single instance of the `FileSystem` class.
`static FileSystem* instance`, means that we instantiate the static local variable for the class. 
This means that it retains its values across function calls. 
Using dynamic memory allocation. 
#localstaticvariables
The static local variable, upon subsequent calls of `instance()`, we just return the same instance. This is because C++ guarantees, that static local variables are initialized only once, regardless of how many times the containing function is called. 
Proper cleanup is fine, and automatic as the program terminates. 

#### WHY? 
Initialized at run time :- 
It doesn't create the instance of no one is using it. 
You can subclass the singleton: 
Subclassing the singleton: 
```
class FileSystem
{ 
public: 
	virtual ~FileSystem() {}
	virtual char* read(char* path) = 0;
	virtual void write(char* path, char* text) = 0;
};

class PS3FileSystem : public FileSystem
{ 
public: 
	virtual char* read(char* path)
	{ 
		// Use Sony file IO API...
	}

	virtual void write(char* path, char* text)
	{ 
		// Use Sony file IO API
	}
};

// Same with WiiFileSystem

```

`FileSystem`, we'll turn that into a singleton: 
```
class FileSystem
{ 
public: 
	static FileSystem& instance(); 

	virtual ~FileSystem() {}
	virtual char* read(char* path) = 0;
	virtual void write(char* path, char* text) = 0;
protected: 
	FileSystem() {}
};
```
Then when we want to create them:
```
FileSystem& FileSystem::instance()
{ 
#if PLATFORM == PLAYSTATION3
	static FileSystem* instance = new PS3FileSystem();
#elif PLATFORM == WII
	static FileSystem* insatnce = new WiiFileSystem();
#endif

	return *instance;
}
```



Remember that static local variables are only initialized once.
Another one: 
```
class Singleton { 
public: 
	// returning a reference to the static object
	static Singleton& getInstance(){ 
		static Singleton instance;
		return instance;
	}

	void doSomething(){ 
		std::cout << "Singleton doing something" << endl;
	}


private: 
	Singleton(){ 
		// Initialize
	}

	~Singleton() {
		// Cleanup Code Here
	}

	// private copy constructor and assignment
	Singleton(const Singleton&);
	Singleton& operator=(const Singleton&);
};


int main(){ 
	Singleton& singleton = Singleton::getInstance();

	singleton.doSomething();
}
```

