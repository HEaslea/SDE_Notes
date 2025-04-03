#openglobjects

The libraries are written in C, so yeah, have fun. 

Objects are a little weird in C remember. 

An object in openGL is a collection of options that represent a subset of openGL's state. 

eg. an object that represents the settings of the drawing window. We can then set its size, how many colours it supports etc. 
We might visualize it like this: 
```
struct object_name{ 
	float option1; 
	int option2;
	char[] name;
};
```

You can imagine, openGL's context as a large struct, a very large struct. 

```
// The State of OpenGL
struct OpenGL_Context
{ 
	... 
	object_name* object_Window_Target;
	...
};
```
Then when we want to use an object, it will look something like this: 
```
// Create Object
unsigned int objectId = 0;

// generating one object, and attaching its ID to objectId
blGenObject(1, &objectId);

// bind/assign object to context
glBindObjecct(GL_WINDOW_TARGET, objectId);

// Then we set the options of object that we just bound to the GL_WINDOW_TARGET
// GL_WINDOW_TARGET is just an option of the context
glSetObjectOption(GLW_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH, 800);
glSetObjectOption(GLW_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);

// set context target back to default
glBindObject(GL_WINDOW_TARGET, 0);
```
This is a frequent thing here: 
We first create an object, and store a reference to it as an id( the real object's data is stored behind the scenes). 
Then we bind the object (using its id) to the target location of the context (the location of the example window object target is defined as GL_WINDOW_TARGET). 
Then we set the the options of that area of the context. 
Then we can unbind the object referenced by `objectId`, and restored as soon as we bind the object back to `GL_WINDOW_TARGET`. 

Remember to `#include <GL/glew.h>` before out `#include <GLFW/glfw3.h>`, as the glew one will include some openGL headers that are required before by the openGL. 

