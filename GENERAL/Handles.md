#handle #handles
Can be anything form an integer index to a pointer to a resource in kernel space. 
The idea is that they provide an abstraction of a resource, so you don't need to know much about the resource itself to use it. 

HWND in the win32 API is a handle for a Window. 
By itself it is useless; you can't glean any information from it. 
But pass it to the right API functions, and you can perform a wealth of different tricks with it. 

You can think of HWND as an index into the GUI's table of windows. 

![[Pasted image 20240403213143.png]]