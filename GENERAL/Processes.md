#### Executable Files
A file that contains a program - a set of instruction that a computer can execute directly. 

**Windows**
- `.exe` (Executable)
- `.bat` (Batch File)
- `.com` (Command File)
- `.msi` (Microsoft Installer)

**MacOS**
- `.app` (Application Bundle)
- Mach - O Binaries

**Linux/Unix**: 
- `ELF` (Executable and Linkable Format)

The EXE files, all of these, for their OSs, have a specific structure that the operating system understands to load and execute the program. 

1. **Header**: Containing metadata, about the file, format, architecture, entry point (where execution begins, in main) and other essential information. 
2. **Sections**: - Code Section, the actual CPU instructions that the CPU executes. 
- Data Section: Static Data used by the program. 
- Resources: Icons, images and other assets. 
3. **Symbol Table**: Information for linking and debugging. 

**Making One Run** 
1. **Invocation**: The user initiates the .exe by double clicking or other methodsx. 
2. **Loading**: the OSs loader will then take over. Reading the Exe's header to understand how to load the file into memory. Allocating Memory space, Loading necessary sections into the allocated memory. Resolving dependencies, such as Linked Libraries (DLLs on windows, Shard Libaries on Linux or macOS). 
3. **Linking**: If the exe relies on external libs, the loader will ensure that those are loaded into memory and addresses are correctly mapped so the program can call these external functions. 
4. **Setting up Execution Environment**: The OS will then set up the stack, heap and other necessary run time environment. Will then initialize variables and resources. 
5. **Transfer of Control:** The OS transfers control to the exe's entry point, which is typically the `main` function or equiv starting point in the program's code. 
6. **Execution**: The CPU beings executing the instruction from the entry point, following the programs logic until it completes or is terminated. 


Executable files are the bridge between human-readable code and machine-executable instructions. 
They encapsulate the necessary information and code for operation system to load and run applications effectively. 
