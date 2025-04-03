[[Singleton Design]]
Singleton usage: 
```
class FileWrapper{
public: 
	FileWrapper() = delete;

	static std::unique_ptr<FileWrapper> init
	(const std::string& filePath)
	{ 
		if(isValid(filePath)) // testing by opening
	}
// destructor, which is vital to close the file on destruction
	~FileWrapper() { CloseFile();}

	void displayPath() const { 
		cout << "Filepath: " << filePath << end;
	}

// simple writing function
	void Write(const string& toAdd)
	{ 
		cout << "Writing:  " << toAdd << "   to: " << filePath << endl;	
		wFile << toAdd << endl;
	}

// logging, using chrono in order to get a string of the current time
	void LogtoFile(const string& extraM)
	{ 
		auto now = std::chrono::system_clock::now();
		std::time_t now_time = std::chrono::system_clock::to_time_t(now);
		std::tm now_tm;
		localtime_s(&now_tm, &now_time);

		std::ostringstream oss;
		oss << std::put_time(&now_tm, "%Y-%m-%d %H:%M:%S  : ");
		wFile << oss.str() << " " << extraM;
	}



// can use this whenever, just part of the class, just to see if any file path is valid
//usable to check validity of any file path
	static bool isValid(const std::string& input)
	{ 
		ifstream ifFile(input);
		if(!ifFile){ 
			ifFile.close(); 
			cout << "File not valid" << endl;
			return false;
		} else { 
			ifFile.close(); 
			cout << "File Valid!" << endl;
			return true;
		}
	}

private: 
	string filePath;
	stream wFile;
	
// private static functions here don't really do anything per se in terms of singletons

// private constructor
	FileWrapper(const string& path) : filePaht(path), 
	wFile(path, std::ios::in | std::ios::out | std::ios::app)
	{ 
		if(!wFile)
		{ 
			cerr << "Failed to open File: " << filePath << endl;
		}
	}

// closing the file 
	void CloseFile()
	{ 
		cout << "Closing File : " << filePath << endl;
		wFile.close();
	}


};
```
When we init, we are instantiating an object, then we are returning the unique_ptr to it. 

TONOTE: The singleton method doesn't really do anything with the private static member function. It still compiles, but doesn't really add anything of substance. 

In general, the idea of static objects means that we can access the function, without instantiating an object of the class itself, which is similar to using namespaces or global functions, and can be replaced with those very things. 
They don't have any implicit `this` pointer, meaning that they cannot access non-static member variables or call non-static member functions directly. 
