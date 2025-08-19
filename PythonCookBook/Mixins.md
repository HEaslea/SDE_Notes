### What? 
**Mixins** are meant to provide reusable functionality to other classes through inheritance, not intended to stand alone. 

Think of it as plugging in extra behaviour, to a class without forcing a rigid hierarchy

Python does this naturally with multiple inheritance. 


```
class LogginMixin: 
	def log(self, message): 
		print(f"[LOG]: {message}")


# timestamp mixin
from datetime import datetime

class TimeStampMixin: 
	def timestamp(self): 
		return datetime.now().isoformat()

class Worker: 
	def work(self): 
		print("Working...")

class LoggedWorker(Worker, LoggingMIxin, TimeStampeMixin): 
	def work(self): 
		self.log("Starting work")
		super().work() # get the one class that does the work 
		self.log(f"Finish working at {self.timestamp()}")

w = LoggedWorker()
w.work()
```

In C++, we don't really have dynamic inheritance model as Python does, using templates is a good option here. 

```
// Logging mixin
template <typename Derived>
class LogginMixin { 
public: 
	void log(const std::string& msg)
	{ 
		std::cout << "[LOG]: " << msg << std::endl;
	}
};

// TimeStamp Mixin
#include <chrono> 
#include <ctime>
template <typename Derived> 
class TimestampMixin { 
public: 
	void timestamp() { 
		auto now = std::chrono::system_clock::to_time_t(std::chrono::system_clock::now())	;
		std::cout << "[TIME]: " << std::ctime(&now);
	}
};

// Worker base class
class Worker { 
public: 
	void work() { 
		std::cout << "Working ... " << std::endl;
	}
};

// Combine Worker with Mixins

class LoggedWorker : public Worker, 
						public LogginMixin<LoggedWorker>, 
						public TimestampMixin<LoggedWorker> 
{ 
public: 
	doWork() { 
		log("Starting Work");
		work();
		log("Finished Work");
		timestamp();
	}
};
```

Python : Just multiple inheritance with small classes. 
C++ : via templates + multiple inheritance, CRTP/Wrapper style -> Curiously Recurring Template Programming.

Composing behaviour without deep inheritance chains. 

[[Advanced Class Design Python]]