## Make
**Build Automation Tool**: 
Using a `makefile` to define how to compile and link code. The build process uses all the rules specified in the Makefile. 
This is super strong, however, making sure that these files are always updated is a pain and annoying. 

## Cmake
**Build System Automation**: 
Generating platform specific build files (Makefiles or Visual Studio Project Files). 
From `CMakeLists.txt`. 
Allowing for cross - platform. 

#### Interaction Between the Two
1. Cmake as a Generator: When running `cmake`, reads the `CMakeLists.txt`, configures project, and generates the appropriate files for the specified build system (like a MakeFile). 
2. Using `make` with the file that `cmake` has creawted, to compile and link your code based on the instructions in that `Makefile`. 


### CMake File 
```
# CMakeLists.txt

# Specify the minimum version fo CMake required
cmake_minimum_required(VERSION 3.10)


# Define the project name
project(MyProject)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 11)

# Add an exe target
add_executable(myprogram main.cpp utils.cpp)

# Optionally, you can include directories
include_directories(include)

# Optionally, you can link libraries
# target_link_libraries(myrpgoram some_library)
```

### Make File Example
```
# Makefile

# Varbiables
CC = g++
CFLAGS = -Wall -g
SOURCES = main.cpp utils.cpp
EXECUTABLE = myprogram

# Default Target
all: $(EXECUTABLE)

# Rule to Build the exe
$(EXECUTABLE): $(SOURCES)
	$(CC) $(CFLAGS) -o $(EXECUTABLE) $(SOURCES)

# Clean up build files
clean: 
	rm -f $(EXECUTABLE)
```