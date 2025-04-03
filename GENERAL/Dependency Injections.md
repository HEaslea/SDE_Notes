[Peter Muldoon Doing Some Great Work Out Here](https://www.youtube.com/watch?v=kCYo2gJ3Y38&t=1384s)


## Basics
- Linking - do not use this anymore - this was so tedious and only works with legacy
- Inheritance / virtual functions
- Templates
- Type Erasure - using `std::function()` etc.


#### Null Value Objects / Stubs
Null value meaning that we are not pointing to any valid memory or resource (`nullptr`), or an object or class that does not have valid or useful state at the moment. 
`nullptr` - indicate an object or resource is unavailable or has not bee initialized yet. 
**Stubs** - piece of code that stands in for incomplete or unimplemented component or function. An object mimicking functionality, usually for testing or development purposes. 
Usually used in unit testing to simulate external systems or components that the code depends on but are either too complex or unavailable for the test. They return predefined values or log calls to methods instead or performing real work, they are just standins essentially, minimal functionality. 
In DI they are used in place of real services, databases, or other components. 
```
class DatabaseConnectionStub
{ 
public: 
	bool connect() 
	{ 
		std::cout << "STUB: simulated db connection" << std::endl;
		return true;
	}

	void query(const std::string&Y query)
	{ 
		std::cout << "STUB: executing query - " << query << std::endl;
	}
};

void useDatabase(DatabaseConnectionStub& db)
{ 
	if(db.connect())
	{ 
		db.query("SELECT * FROM users");
	}
}
```

There is also the idea of a null object pattern design, which just mimics the idea of an empty object. 

### Using Inheritance and Virtual Functions
```
class Logger
{ 
public:
	virtual void log(const std::string& message) = 0;
	virtual ~Logger() = default;
};

class ConsoleLogger : public Logger 
{ 
public: 
	void log(const std::string& message) override
	{ 
		std::cout << "CONSOLE LOGGER" << std::endl;
	}
};

class FileLogger : public Logger
{ 
public: 
	void log(const std::string& message) override
	{ 
		std::cout << "FILELOGGER" << std::endl;
	}
};

class Application
{ 
private: 
	std::shared_ptr<Logger> logger; // dependency is injected 

public: 
// then just with ctor here
	Application(std::shared_ptr<Logger> logger) : logger(std::move(logger)) {}


	void doWork()
	{ 
		logger->log("DOING SOME");
	}
};
```

This does promote the open closed principle (open for extension, closed for modification). 
Decoupling, as we are only relying on the interface and we do not need to know how that class implements, from just using it. 
There is ease of just putting stubs or mock objects into the inheritance hierarchy etc. 
This is easier to add to older codebases. 

The drawbacks are that the interface can become messy, or has purely test functions added. 
Of course there is the idea that virtual functions have that "extra hop" to get to the actual function. 

## Templates
Compile time, therefore no runtime overhead. Concepts can even define a better "interface". 
```

// then with concepts
template <typename T> 
concept CalcE£ngineT = rquires (T t, const Data& d)
{ 
	{t.calculate(d)} -> std::convertible_to<bool>;
	{t.apply(d)} ->std::convertible_to<bool>;
}

template <typename CalcEngine> 
bool process(CalcEngine& engine)
{ 
	// ...
	engine.apply(rdata);
	rdata.data = "2";
	// ...
	return engine.calculate(rdata)/ 
}

struct RealCalcEngine
{ 
	RealCalcEngine(...);

	bool apply(const Data& rdata);
	bool calculate(const Data& rdata);
};

struct TestCalcEngine
{ 
	TestCalcEngine();
	// ...
}
```
The concept will make us lose flexibility. 

#### Setter Dependency Injection
DI - dependencies are provided to an object through setter methods rather than being passed through the constructor, allowing for a more dynamic approach to DI, without having to make a new Object every single time we want to change functionality. 
This also means that we can inject after the objects creation. 
This is great when we have optional dependencies. 

```
class ILogger
{ 
public: 
	virtual void log(const std::string& message) = 0;
	virtual ~ILogger() = default; // always remember this
};

// concrete logger Impl
class ConsoleLoger : public ILogger
{ 
public: 
	void log(const std::string& message) override
	{ 
		std::cout << "Console Logger: " << message << std::endl;
	}
};

class FileLogger : public ILogger
{ 
public: 
	void log(const std::string& message) override
	{ 
		std::cout << "File Logger: " << message << std::endl; // simulating writing to files
	}
};

class App
{ 
private: 
	std::shared_ptr<ILogger> logger; // dependency usin virtual
	
public: 
	void setLogger(std::shared_ptr<ILogger> logger)
	{ 
		this->logger = std::move(logger);
	}

	void run()
	{ 
		if(logger)
		{ 
			logger->log("Application Started");
		} else 
		{ 
			std::cout << "NO LOGGER AVAILABLE: " << std::endl;
		} 	
	}
};

int main() 
{ 
	Application app; // nothing going into constructor, shared_ptr is null right now
	
	auto consoleLogger = std::make_shared<ConsoleLogger>();
	app.setLogger(consoleLogger);
	app.run(); // Ouput console logger : application started

	auto fileLogger = std::make_shared<FileLogger>();
	app.setLogger(fileLogger);
	app.run(); 
}
```
Remember that when we move from a `shared_ptr`, this original will no longer provide towards the reference count. 

