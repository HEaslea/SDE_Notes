The code and then going through each section: 
```
// some .h file
#pragma once
#include <mutex>
#include <fstream>
#include <chrono>
#include <string>
#include <string_view>
#include <thread>
#include <iostream>
#include <source_location>
#include <format> 

enum class LogLevel { trace, debug, info, warn , error};

class Logger { 
	std::mutex mu_; // the Logger mutex
	std::streambuf* out_;
	LogLevel  min_level_ = LogLevel::info;

	static std::string level_name(Loglevel l) 
	{ 
		switch(l)
		{ 
			case LogLevel::trace: return "TRACE";
			case LogLevel::debug: return "DEBUG";
			case LogLevel::info:  return "INFO";
			case LogLevel::warn:  return "WARN";
			case LogLevel::warn:  return "WARN";
			case LogLevel::error: return "ERROR";
		}
		return "?"
	}

	static std::string time_point_to_string(std::chrono::milliseconds tp)
	{ 
		using namespace std::chrono; 
		auto t = system_clock::time_point(tp);
		std::time_t tt = system_clock::to_time_t(t);
		auto ms = tp.count() % 1000;
		std::tm bt{};
	#if defined(_WIN32)
		localtime_s(&bt, &tt);
	#else
		localtime_r(&tt, &bt); 
	#endif
		char buf[32];
		std::snprintf(buf, sizeof(buf), "%04d-%02d-%02d %02d:%02d:%02d.%03d", 
			bt.tm_year+1900, bt.tm_mon+1, bt.tm_mday, 
			bt.tm_hour, bt.tm_min, bt.tm_sec, int(ms));
		return buf;
	}
	
	static std::string thread_id_to_string(std::thread::id id)
	{ 
		std::ostringstream oss; oss << id; return oss.str();
	}

public: 
	explicit Logger(std::ostream& sink) : out_(sink.rdbuf()) {}

	template<class ... Args> 
	void log(LogLevel lvl, 
		std::string_view fmt, 
			Args&& ... args, // universal reference
			const std::source_location loc = std::source_location::current())
	{ 
		if(!enabled(lvl)) return;
		
		auto now    = std::chrono::system_clock::now();
		auto time   = std::chrono::floor<std::chrono::milliseconds>(now);
		auto tid    = std::this_thread::get_id(); // getting the thread id

		std::lock_guards<std::mutex> lock(mu_); // using class mutex

		std::ostream os(out_);

		os << std::format("{} [{}] [{}] {}:{} {} | ", 
				time_point_to_string(time), 
				level_name(lvl),
				thread_id_to_string(tid),
				loc.file_name(), loc.line(), 
				loc.function_name()
		);

		os << std::vformat(fmt, std::make_format_args(std::foward<Args>(args)...)) << "\n";
		os.flush();
	}
	
	void set_level(LogLevel lvl) { min_level_ = lvl; }
	bool enabled(LogLevel lvl) const { return static_cast<int>(lvl) >= static_cast<int>(min_level);}
};

#define LOG_INFO(lg, fmt, ...) (lg).log(LogLevel::info,  (fmt), ##__VA_VARGS__)
#define LOG_WARN(lg, fmt, ...) (lg).log(LogLevel::warn,  (fmt), ##__VA_VARGS__)
#define LOG_ERR(lg,  fmt, ...) (lg).log(LogLevel::error, (fmt), ##__VA_VARGS__)
```

Going through the various parts of the code.
##### `__VA_ARGS__`
A preprocessor trick to handle variable arguments and avoid that pesky extra comma. 
I wasn't aware that you could write variadic macros:
```
#define LOG(fmt, ...) printf(fmt, __VA_ARGS__)


// then that can be called like this: 
LOG("HELLO %s\n", "world");

// however, there has to be extra arguments, we will get an error
LOG("Hello \n"); // Trailing comma before ), as in an extra comma before )
// as this expands to printf("Hello\n", ); which is of course invalid C++
```

The `##` is the token pasting operator for macros, and will get rid of the trailing comma if there is one.
This takes care of what we were writing about just here. 

In general, the `##` takes the what's on the left and glues it to whatever's on the right. 
```
#define REG_VAR(name) int var_##name = 0
REG_VAR(foo); // turns into : int var_foo = 0;
```

```
#define ERROR_CODE(name) ERR_##name
int code = ERROR_CODE(NOT_FOUND); // turns into ERR_NOT_FOUND
```
And also removing the commas like earlier: 
```
#define PRINT(fmt, ...) printf(fmt, ##__VA_ARGS__) // anything with __ is likely to be a macro trick
print("HELLO\n"); // Works find with no trailing comma
```

### StreamBuf
[Reference for streambuf](https://cplusplus.com/reference/streambuf/streambuf/)
In charge of the reading and writing operations of the stream object it is associated with : the stream delegates all such operations to its associated `stream buffer` object. 
It can manage any of these `std::cout`, `std::cin` and even `std::ifstream` making it a prime candidate for a logger that could use nay of these. 

`std::basic_streambuf` -> that is the template base, specialized as `std::streambuf` `char` and `std::wstreambuf` for `wchar_t`. 

And can be subclassed in order to add more features to it. 

### Time Point and System Clock
Utilizing something called the `epoch`, for Unix and C++, `system_clock` is from `00:00:00` on 1 Jan 1970. 
This just sounds super weird. 
However, very efficient, as from `epoch`, is around 1.76 quintillion, which still fits into a 64 bit integer. 
The overflow will be around 2038, so that's our Deus Ex Machina for the AI takeover. 
The trouble with it just converting it to a human readable form, which is just lazy evaluated when we need it. 
And the maths is really simple, as we take a time in whatever time frame we want and then do the arithmetics around it. 

The code above: 
We take in `std::system_clock::milliseconds`

`auto time   = std::chrono::floor<std::chrono::milliseconds>(now);`
`floor` just returns a rounded down duration that is < or == to `d` given. 


```
template<class ... Args> 
	void log(LogLevel lvl, 
		std::string_view fmt, 
			Args&& ... args, // universal reference
			const std::source_location loc = std::source_location::current())
	{ 
		if(!enabled(lvl)) return;
		
		auto now    = std::chrono::system_clock::now();
		auto time   = std::chrono::floor<std::chrono::milliseconds>(now);
		auto tid    = std::this_thread::get_id(); // getting the thread id

		std::lock_guard<std::mutex> lock(mu_); // using class mutex

		std::ostream os(out_);

		os << std::format("{} [{}] [{}] {}:{} {} | ", 
				time_point_to_string(time), 
				level_name(lvl),
				thread_id_to_string(tid),
				loc.file_name(), loc.line(), 
				loc.function_name()
		);

		os << std::vformat(fmt, std::make_format_args(std::forward<Args>(args)...)) << "\n";
		os.flush();
	}
```

Template `Args` packing. 
Then we have the `LogLevel l`  
`std::source_location` auto captures caller's file, line and function

Given that we are using threads, we get the thread id, `std::this_thread::get_id()`. 
As we can output that as well. 

Then we lock so that we can use the `out_`, remembering that that is the `std::streambuf*` as the member variable. 

And then we out with `std::format`. 

I feel like all of this is doable, however, knowing all the weird functions, such as `vformat` just takes some experience.

```
std::format("Hello {}!", name);

auto args = std::make_format_args(name);
std::string s = std::vformat("Hello {}!", args);
```
`std::string vformat(std::string_view fmt, std::format_args args);`
Then we get the result as a `std::string`, which is just so fantastic. 

eg. 
```
template<typename ... Args> 
inline void println(const std::format_string<Args...> fmt, Args&& ... args)
{ 
	std::cout << std::vformat(fmt.get(), std::make_format_args(args...)) << "\n";
}

println("{}{} {}{}", "Hello", ',',  "C++", -1 + 2 * 3 * 4);

// will give, Hello, C++23
```

