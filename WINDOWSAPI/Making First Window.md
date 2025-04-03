[Making First Window](https://learn.microsoft.com/en-us/windows/win32/learnwin32/your-first-windows-program)

We'll start by looking at the complete code of the program: 
```
#ifndef UNICODE
#define UNICODE
#endif

#include <windows.h>

LRESULT CALLBACK WindowProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam);

  

int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PWSTR pCmdLine, int nCmdShow)

{

    // Register the window class.

    const wchar_t CLASS_NAME[]  = TEXT("Sample Window Class");

    WNDCLASS wc = { }; // default initialization

  

    wc.lpfnWndProc   = WindowProc;

    wc.hInstance     = hInstance;

    wc.lpszClassName = CLASS_NAME;

  

    RegisterClass(&wc);

  

    // Create the window.

  

    HWND hwnd = CreateWindowEx(

        0,                              // Optional window styles.

        CLASS_NAME,                     // Window class

        L"Learn to Program Windows",    // Window text

        WS_OVERLAPPEDWINDOW,            // Window style

  

        // Size and position

        CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,

  

        NULL,       // Parent window    

        NULL,       // Menu

        hInstance,  // Instance handle

        NULL        // Additional application data

        );

  

    if (hwnd == NULL)

    {

        return 0;

    }

  

    ShowWindow(hwnd, nCmdShow);

  

    // Run the message loop.

  

    MSG msg = { };

    while (GetMessage(&msg, NULL, 0, 0) > 0)

    {

        TranslateMessage(&msg);

        DispatchMessage(&msg);

    }

  

    return 0;

}

  

LRESULT CALLBACK WindowProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam)

{

    switch (uMsg)

    {

    case WM_DESTROY:

        PostQuitMessage(0);

        return 0;

  

    case WM_PAINT:

        {

            PAINTSTRUCT ps;

            HDC hdc = BeginPaint(hwnd, &ps);

  

            // All painting occurs here, between BeginPaint and EndPaint.

  

            FillRect(hdc, &ps.rcPaint, (HBRUSH) (COLOR_WINDOW+1));

  

            EndPaint(hwnd, &ps);

        }

        return 0;

  

    }

    return DefWindowProc(hwnd, uMsg, wParam, lParam);

}
```
Now if you copy and paste this, you will get an error. However, stay the course, read through what each thing is and we can see about fixing it. 

The overview here is: 

1. `wWinMain` is the program entry point. When the program starts, it registers some information about the behaviour of the application window. One of the most important things is the address of a function named `WindowProc`. This function defines the behaviour of the window - its appearance, the way it interacts with the user, and so forth. 
2. Then we create a window and the program receives a handle that uniquely identifies the window. 
3. If it does make it successfully, the program enters a while loop. It remains here until the user closes the window and exits the application.

Windows will communicate with your program by passing it a series of *messages*. 
The code inside the `while` loop drives this process. 

Each time the program calls the `DispatchMessage` function, it indirectly causes Windows to invoke the `WindowProc`, once for each message. 

### Creating A Window
A window class defines a set of behaviours that several windows might have in common. 

Data that is unique for each window is called `instance data`. (Essentially their data members of their class). 

Every window must be associated with a window class, even if you only create one instance of that class. 
A window isn't a class in the C++ sense. 
Rather, it's a data structure used internally by the operation system. 
Window classes are registered with the system at run time. 
To register a new once, fill in a `WNDCLASS` structure. 

```
// Register the window class
const wchar_t CLASS_NAME[] = L"Sample Window Class";

WNDCLASS wc = { }; // default initialization of that class

wc.lpfnWndProc   = WindowProc;
wc.hInstance     = hInstance; 
wc.lpszClassName = CLASS_NAME;
```

`lpfnWndProc` is a pointer to the application defined function called the `window procedure` or `window proc`. This defines most of the behaviour by the window. 

`hInstance` is the handle to the application instance. Get this value from the `hInstance` parameter of `wWinMain`. 

`lpszClassName` is used to identify the window class. 

The name here only needs to be unique within the process. 
Do not conflict with the Windows controls classes eg. `Button`. 

`WNDCLASS` has other members that are not shown here. You can set them to 0, or fill them in, you can see the class. 
Then we pass the address to the `RegisterClass()` function. This registers the window class with the operation system. 