"Programme to Interface not to Implementation"

```
// Abstract Class of Logger
class Logger{ 
public: 
	virtual void log(const string& message) = 0; // pure virtual 
	virtual ~Logger() = default;
};

class ConsoleLogger : public Logger{ 
public: 
	void log(const string& message) override
	{ 
		cout << "Console Log: " << message << endl;
	}
};

class FileLogger : public Logger{ 
public: 
	void log(const string& message) override
	{ 
		logFile << "File Log: " << message << endl;
	}
};

// using the interface
class Application {
private: 
	Logger& logger;

public: 
	Application(Logger& logger) : logger(logger) {}

	// just some Application Logic Here
	void run(){ 
		logger.log("APP STARTED");
		...
	}
};

int main(){ 
	ConsoleLogger consoleLogger; 
	Application app(consoleLogger);

	app.run(); 

	// in order to change logging implementation, simply pass a different logger
	Filelogger fileLogger;
	Application appWithFileLogger(fileLogger);
	appWithFileLoger.run(); // will go to file

	return 0;
}
```

