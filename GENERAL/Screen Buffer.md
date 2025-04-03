#screenbuffer
2D array holding character and color data for output in a console window. 
A console have multiple screen buffers. 
The active screen buffer is the one that is displayed on screen. 

We create a screen buffer whenever we create a new console. 
To open a handle to a console's active screen buffer -> `CreateConsoleScreenBuffer` function, which can open additions screen buffers for the console. 
`HANDLE hConsole = CreateConsoleScreenBuffer(GENERIC_READ | GENERIC_WRITE, 0, NULL, CONSOLE_TEXTMODE_BUFFER, NULL)`


We then have to create an active screen buffer: 
`SetConsoleActiveScreenBuffer(hConsole);`
