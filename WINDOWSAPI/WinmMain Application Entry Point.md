Every windows program includes an entry-point function named either `WinMain` or `wWinMain`. 

```
int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance
PWSTR pCmdLine, int nCmdShow);
```

The parameters here are: 
- `hInstance` is the handle to an instance or handle to a module. The operating system uses this value to identify the executable or EXE when it's loaded in memory. Certain windows functions need the instance handle, eg. to load icons or bitmaps. 
- `hPrevInstance`, has no meaning anymore lol, it was used in 16 bit, has no meaning now, always set to 0. 
- `pCmdLine` contains the command line arguments as a Unicode string. 
- `nCmdShow` is a flag that indicates whether the main application window is minimized, maximized, or shown normally. 

The function returns an `int`. 
We can use this to pass to another program. 

A `calling convention` such as `WINAPI`, defines how a function receives parameters from the caller. It changes the order of the parameters on the stack. 

Now how does the compiler know to call the `WinMain` or `wWinMain` when usually you call a main. 
Well the Microsoft C runtime library (CRT) provides an implementation of `main` that calls either `WinMain` or `wWinMain` depending of course. 

Before this, it will call any static initializers. 

Here is an empty `WinMain` function. 
```
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, 
	PSTR lpCmdLine, int nCmdShow)
{ 
	return 0;
}
```

Now we are ready to make our first window apparently. 
