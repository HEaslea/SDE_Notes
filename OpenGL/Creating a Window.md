[Link For This](https://learnopengl.com/Getting-started/Creating-a-window)
[GLFW docs](https://docs.gl)

There is documentation, read it. 

Cherno has said that GLFW is a state machine. 

Some part of OpenGL will come with windows, as we are working directly with the GPU. 

I'm using GLEW: in order to use the functions within OpenGL, we have to be able to find them on that machine. 
We're not linking directly with GL functions directly. 
Some OpenGL functions and drivers come from various places, therefore we use GLEW in order to find them at run-time, and then we implement them through a series of pointers. 

GLEW needs to know how we are linking to the OpenGL file that we are linking with visual studio, this means that we have to go into the pre-processor instructions of VS, and state `GLEW_STATE`. 

![[Pasted image 20240601200052.png]]

The Cherno Video on Setting up OpenGL was really good at explaining how to set up a dependency folder, that holds all the includes within the solution folder. 
![[Pasted image 20240601200152.png]]

This is in the Linker - Input - Additional Dependencies section: 

There are other parts here, that's where the other OpenGL drivers and functions are found. 

`opengl32.lib` - is the one that we included.
`Gdi32.lib` - where to find at least one openGL driver. 
etc. 
`glew32s.lib` - the static version of the lib, will be much bigger than the dynamic one. 

GLEW therefore needs to know which to use, static or dynamic. 
Try and always use static if you can according to Cherno. 

The thing with GLEW is that the include needs to come before the GLFW include, as it needs to set everything up before GLFW. 
GLFW will also disable further inclusion, therefore it needs to come before. 

```
#include <GL/glew.h>
#include <GLFW/glfw3.h>
```

#### Setting up a Context for GLEW
We need to use `glewInit()`. 
This will need to come after we have set up the GLFW context: 

There are these steps before we do that: 
```
if(!glfwInit())
	return -1;

window = glfwCreateWindow(1280, 720, "Hello World", NULL, NULL);
if(!window)
{ 
	glfwTerminate();
	return -1;
}

/* make the window's context current */
glfwMakeContextCurrent(window);

// NOW WE SET UP GLEW
if(glewInit() != GLEW_OK)
	std::cout << "NOT OKAY" << std::endl;
```

```
GLFWwindow* window = glfwCreateWindow(800, 600, "Hello World", NULL, NULL); 

if(!window)
{ 
	glfwTerminate(); 
	return -1; // just terminate the bitch 
}
```
![[Pasted image 20240601202054.png]]![[Pasted image 20240601202121.png]]
Then we have to make context: before we can make OpenGL calls, we need to have the right "Context" of the correct type. 
A context can only be current for a single thread at a time, and a thread can only have a single context current at a time. 
They are essentially a state machine that stores all data related to the rendering of your application. 
A window - is something that you can draw to (a drawable). 
A context is sort of the machine that is doing the drawing. 

There is no **real strong** connection between a window and a context. 
After a context has been successfully created, it can be used with any window that has been configured to a compatible pixelformat. 

It's an object that is like the whole OpenGL thing. 

Remember here that GLFW is an open source, multi-platform library for OpenGL, OpenGL ES and Vulkan. 

So let's make a context (an object with all the moving parts): 
```
glfwMakeContextCurrent(window);
```

Then we are about ready to create a window. 

We can then set up the `viewport`, we have to tell OpenGL the size of the rendering window so OpenGL knows how we want to display the data and coordinates with respect to the window. 


Then we loop: 
```
while(!glfwWindowShouldClose(window))
{ 
	glfwSwapBuffers(window);
	glfwPollEvents();
}
```

Swapping buffers is just to change what is on the screen. 

Then right to the end: 
```
glfwTermiante();
return 0;
```
This will just clear everything, a big sweep. 


```
#include <GL/glew.h>
#include <GLFW/glfw3.h>

#include <iostream>

// callback for poll events
// callbacks are functions passed into another as an argument, invoked inside the outer function to complete some kind of routine or action

void frambuffer_size_callback(GLFWwindow* window, int width, int height);
void processInput(GLFWwindow* window);

int main()
{ 
	// glfw: init and conigure
	glfwInit(); 
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	
#ifdef __APPLE__
	glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
#endif

	// glfw window creation
	GLFWwindow* window = 
		glfwCreateWindow(1280, 720, "First Window", NULL, NULL);

	if(window == NULL)
	{ 
		cout << "Failed to create GLFW window" << endl;
		glfwTerminate();
		return -1;
	}

	// creating a context "machine" for the window
	// making it "current"
	glfwMakeContextCurrent(window);
	
	// Not sure what this does
	glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

	// with context set up, we can init GLEW
	if(glewInit() != GLEW_OK)
		cout << "GlewInit Failed" << endl;


	// Here's our loop
	// WindowShouldClose returns false if we keep it over
	while(!glfwWindowShouldClose(window))
	{ 
		// process input 
		processInput(window);


		// swapping buffers and poll IO events 
		// key press/release mouse moved etc.
		glfwSwapBuffers(window);
		glfwPollEvents(); 
	}
	
	return 0;
}


// process all input: query GLFW, whether relevant keys are pressed/released this frame and react
void processInput(GLFWwindow* window)
{ 
	if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldClose(window, true);
}

// glfw: whenever the window size changed, this callback function executes
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{ 
	glViewport(0, 0, width, heigh);
}
```

##### Input
We want some form of input control, and can achieve this with several of GLFW's input functions. 

We'll use `glfwGetKey` function that takes the window as input together with a key. 
```
void processInput(GLFWwindow * window)
{ 
	if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldCLose(window, true);
}
```
So if escape is not being pressed, then `glfwGetKey` will return `GLFW_RELEASE`
This process input will have to be checked every frame. 

#### Rendering
Must be placed in loop, each "frame" we render. 

 We can clear the buffer every frame too, and we can clear only certain types found in the buffer.  


#### Binding the Buffers
When we: 
```
unsigned int buffer;
glGenBuffer(1, &buffer); // creating one buffer
// the name of the buffer is buffer
// buffer is the identifier for that buffer object
```
Here we are merely creating a buffer object, with a buffer object name. 

We can then pop this into GPU memory by using `glBindBuffer`, binding the buffer, specifying the data that it holds. 

`glBindBuffer(GL_ARRAY_BUFFER, buffer)`

`GL_ARRAY_BUFFER` here is for vertex attributes. 

Then when we know what info we want in that buffer, we get to adding it. 

```
GLfloat vertices[] = { 
	0.0f, 0.5f, 0.0f, 
	-0.5f, -0.5f, 0.0f, 
	0.5f, -0.5f, 0.0f
}; // Note that these are all positions and nothing more

glBufferData(GL_ARRAY_BUFFER, sizeof(float) * 9, vertices, GL_STATIC_DRAW);
```

As we have bound the buffer from earlier to the type of `GL_ARRAY_BUFFER`, from this point on any buffer operations on the `GL_ARRAY_BUFFER` will affect the buffer object associated with `buffer`. 
This is a specific type of memory in the GPU, where vertex attribute data is stored. 
![[Pasted image 20240602000425.png]]