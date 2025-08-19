
Where do we put files when it comes to file paths
## System-wide Installed Apps
Config files can go in well known OS-specific Directories: 
**Linux**:  `/etc/myapp/config.conf` or `~/.config/myapp/config.conf`
`~` in Linux is the home directory: `~` -> `/home/alice` or if you are the root user it's `/root`.
However, putting that into C++.

**MacOS**: `~/Library/Application Support/MyApp/config.conf`

**Windows**: `%APPDATA%\MyApp\config.conf`

Making the binary location irrelevant, and we always know where to look. 

## Self-contained Distribution (binary + configs together)
Config files that are placed relative to the binary, eg. `./config/config.conf`. 
	The binary will resolve its own location at runtime using `argv[0]`. 
Very common in games. 


## Developing and Testing
Often relative to the working directory, during development, as IDE's let you set a working directory. 

In production, switch to absolute or binary relative paths. 

### Best Practice
Search order: 
1. Command Line Path (Highest Priority)
2. Environment Variable (`MYAPP_CONFIG`)
3. Binary-relative Path (`./config/config.conf`)
4. OS Standard Path (`/etc/myapp/config.conf/`or `%APPDATA%\MyApp`)

Then we log exactly where the config is being loaded from at startup. 

### The Basics
When we do `std::ifstream`, we can give a path. 
If we do `std::ifstream f("data.txt")` it will only look in the current working directory. 
This is the directory that we call the binary from, not where the binary is stored. 
If we have the config files, or a file we read from next to the binary, not relative to the working directory, which would be crazy. 
We need to get the binary's directory at run time and build the path from that. 

**Writing**: 
`std::ofstream out("out.txt")` -> will create the file if there is not one there. If the file does exist, then we have different types of modifiers for that file. 

**Absolute vs Relative Paths**: 
Absolute paths look like `/home/alex/data.txt` or `C:\\Users\\Alex\\data.txt`

Relative: `data.txt` `configs/app.conf` - this is resolved against the CWD. 

If we call the binary from the terminal, the CWD will be where we called it from. 

**`<filesystem>`**  : 
Is always going to be safer. 
And we can use `namespace fs = std::filesystem;` in order to shorten the call there. 

```
fs::path p = "logs/app.log";

fs::create_directories(p.parent_path()); // make sure folders exist

std::ofstream out(p);

if(!out) { std::cer << "open fail: " << p << "\n"; return 1;}
```

Go to `std::filesystem` to see what is possible, and `std::filesystem::path` in order to see how this works too. 

`parent_path()` will return the path to the parent dir. 

[std::filesystem::path](https://en.cppreference.com/w/cpp/filesystem/path.html)

There are some very important things on here, such as `root_path` and `relative_path`. 

`filesystem::path` will also automatically handle the separators on other systems as well. 

We are able to get the current working directory quick easily using `std::filesystem::current_path()`. 

You could let users put in their own path: 
```
const char* path = (argc > 1)? argv[1] : "data.txt"; // if arg1, name of binary is first
std::ifstream in(path); 
```

Getting the path to the binary, this will be OS dependent, there is no one stop shot to get to the final path to the binary. 

If we make a file that is relative to the binary, then we can hard code the relative path here. 

eg. `filesystem::path cfg = exe_dir() / "configs" / "config.conf";`

This way, `path` can use the `operator/` in order to join the path together based on the system that we are on. 

`/` on linux and `\` on Windows (why Windows?). 

Doing this in raw C++ is really awkward and there are libs that would make this easier. 

**Windows**: 
```
#include <windows.h>

fs::path exe_dir() 
{ 
	wchar_t buf[MAX_PATH];
	// some legacy style stuff here
	DWORD len = GetModuleFileNameW(nullptr, buf, MAX_PATH); 
	if(len == 0) throw std::runtime_error("GetModuleFileNameW failed");
	reteurn fs::path(buf).parent_path(); // strip the exe name
}
```

**Linux**: 
```
#include <unistd.h>

fs::path exe_dir() 
{ 
	char buf[4096];
	ssize_t n = readlink("/proc/self/exe", buf, sizeof(buf) - 1); // not a clue 
	
	if(n == -1) throw std::runtime_error("readlink failed");
	buf[n] = '\0';
	return fs::path(buf).parent_path();
}
```



