![[Pasted image 20240407205558.png]]

It may be surprising that UI controls, such as buttons and edit boxes, are themselves windows. 
UI control cannot exist by itself. 
Control is positioned in relation to the application window. 

When you drag the application window, the control will move with it. 

Control and Application can communicate with each other. 
ie. click notification from a button. 

Moreover a window is: 
- Occupies a certain portion of the screen
- May or may not be visible at a given moment
- Knows how to draw itself 
- Responds to an even from the user or the operating system

### Parent Windows / Owner Windows 
With UI control, the child of the application window. 
The application window is the parent of the UI control. 
The parent windows provides the coordinate system used for positioning a child window. 

A child window cannot exist outside the owner window. 

Application window and a model dialog window. 
![[Pasted image 20240407210202.png]]
The application window here is the owner, the model dialog box is the owned window. 

The dialog window is the parent of both buttons. 
![[Pasted image 20240407210427.png]]

### Window Handles
#handle
Windows are objects, they have both code and data - but they are not C++ classes. 
A program needs to reference a window by using a value called a `handle`. 
It's a number that the OS uses to identify an object. 
Windows has a big table of all the windows that have been created. 
It uses this table to look up windows by their handles. 
The data type for window handles is `HWND`. 
This is returned by the functions that create windows: 
`CreateWindow` and `CreateWindowEx`. 
To perform an operation on a window, we typically call some function that takes a `HWND` value as a parameter. 
We can reposition the window on the screen as so: 
```
BOOL MoveWindow(HWND hWnd, int X, int Y, int nWidth, int nHeight, BOOL bRepaint);
```

Parameters: the first is the handle to the window that you want to move. 
The others are to specify the location of the window and whether the window should be redrawn. 

### Screen and Window Coordinates
Coordinates are measured in device-independent pixels. 
![[Pasted image 20240407211311.png]]