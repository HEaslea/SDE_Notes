With trivial understanding: 

```
#include <GL/glew.h>
#include <GLFW/glfw3.h>

const char* vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos; \n"
"void main()\n"
"{\n"
"	gl_position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";

const char* fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"	FragColor = vec4(1.0f, 0.5f, 0.2f, 0.1f);\n"
"}\0";




int main() {
	
	glfwInit();

	GLFWwindow* window = glfwCreateWindow(1280, 720, "Small Window", NULL, NULL);

	if (window == NULL) {
		std::cout << "Failed to Create Window" << std::endl;
		glfwTerminate(); 
		return -1;
	}

	glfwMakeContextCurrent(window);

	if (glewInit() != GLEW_OK)
	{
		std::cout << "GLEW init failure" << std::endl;
		return -1;
	}


	float vertices[] = {
		-0.5f, -0.5f, 0.0f, 
		0.5f, -0.5f, 0.0f, 
		0.0f, 0.5f, 0.0f 
	};

	unsigned int VAO;
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);


	unsigned int VBO;
	glGenBuffers(1, &VBO);

	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(float) * 9, vertices, GL_STATIC_DRAW);
	

	unsigned int vertexShader;
	vertexShader = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
	glCompileShader(vertexShader);
	
	unsigned int fragmentShader;
	fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
	glCompileShader(fragmentShader);

	unsigned int shaderProgram;
	shaderProgram = glCreateProgram();
	glAttachShader(shaderProgram, vertexShader);
	glAttachShader(shaderProgram, fragmentShader);

	glLinkProgram(shaderProgram);

	glDeleteShader(vertexShader); glDeleteShader(fragmentShader);
	
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	glUseProgram(shaderProgram);
	glBindVertexArray(VAO);

	

	while (!glfwWindowShouldClose(window))
	{
		// input
	
		// render
		glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);
		glDrawArrays(GL_TRIANGLES, 0, 3);


		glfwSwapBuffers(window);
		glfwPollEvents();
		std::this_thread::sleep_for(std::chrono::milliseconds(50));
	}


	glfwTerminate();
	return 0;
}
```

Started with the source for the vertex shader source code, that uses GLSL: 
Vertex shading gives us a good idea of the index of each attribute in the vertices that we have passed. 

##### GLSL: A C-like language

Shaders, small programs that run on the GPU. 
They control the graphics pipeline, and render images, we manipulate vertices and pixels on the screen. 

The order of creating a shard, either fragment, or vertex they work the same way, therefore we can put them into a single function that we call just with different Shader Types:

`GLuint or unsigned int shader = glCreateShader(GL_VERTEX/FRAGMENT_SHADER);// resolving to just an int`. 

Giving that object the source code that we have written
```
glShaderSource(shader, 1, &shaderSource, NULL);
```
Just linking the source, which needs to be available through the address. 
Then we just need to compile it, and we have our `GL_VERTEX_SHADER`. 
`glCompileShader(shaderu)`. 


We can then check for errors: 
```
int succes;
char infoLog[512];
glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
if(!success)
{ 
	glGetShaderInfoLog(shader, 512, NULL, infoLog);
	std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
	<< infoLog << std::endl;
}
```

Fragment shaders just have a different source code, I'm not 100% sure how it's to work yet. 

Then we follow this with the idea of a program, the shading program that our GPU will use later. 

#### Preliminaries
```
glfwInit();

GLFWwindow* windwo = glfwCreateWindow(width, height, "Name", NULL, NULL);
// the last two are kept null for now, they have properties, check dos.gl

// window check 
if(window == NULL)
{ 
	cout << "WINDOW CREATION ERROR" << endl;
	glfwTerminate();
	return -1;
}
```

Making this window the current context, ie. the statemachine to be focussing on the window. 
Context = state machine workings, we change those bindings and values, then run, and the program runs through those series of values and bindings. 

`glfwMakeContextCurrent(window);`

##### Init GLEW
GLEW-ing the whole thing together, remember that GLEW is for all the gathering of the OpenGL drivers and functions that are sort of scattered about the machine. 

Then, you kind of have everything set up in terms of the dependencies: 


#### The Window Loop
```
while(!glfwWindowShouldClose(window))
{ 
	// input
	// render

	// set clear colour for colour buffer 
	glClearColour(/* four floats of whatever you want */ );
	glClear(GL_COLOUR_BUFFER_BIT);


	// necessary
	glfwSwapBuffers(window);
	glfwPollEvents();
	// if you have sleep actions, do them here
}
```


### Creating Useful Vertices
There is the VBO, the vertex buffer object, which is just the data.
And the VAO, vertex array object, that will tell the GPU/shader how to read the VBO. 

As usual, when we create something, it needs its ID. 

First, let us create the actual vertices that we are going to be using, here we are just creating the positions, no colour, nothing else, just the position vector of the vertex: eg.
Even then we are just using the x, y, z is just being init'd to 0. 
```
float vertices[] = { 
	-0.5f, -0.5f, 0.0f, 
	0.5f, -0.5f, 0.0f, 
	0.0f, 0.5f, 0.0f 
};
```

Then we create the buffer for it, which is an object holding that data, it doesn't describe how we are going to use it yet: 
```
unsigned int VBO; 
glGenBuffers(1, &VBO);

// then bind to SM's GL_ARRAY_BUFFER
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(float) * 9, vertices, GL_STATIC_DRAW);

// now everytime that we see GL_ARRAY_BUFFER we are talking about the vertices that we added in
```

Then we need the VAO, which will tell the shader how to interpret that data that we have bound: 
```
unsigned int VAO; 
glGenVertexArrays(1, &VAO); // generate Vertex Array Object
glBindVertexArrays(VAO);

// how do we interpret the data: 
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
```
The only thing here is to look at the documentation: 
[glVertexAttribPointer gl4 Documentation](https://docs.gl/gl4/glVertexAttribPointer)

Then we need to enable this, this is because we can have multiple of these objects, and can enable and disable them as we go, meaning that we can rapidly change how we interpret the data in the buffer, without having to write this out every time. 

### Creating a Shading Program
Now that we have compiled both shaders, we need to link them, for the program that the GPU will use. 
This is really easy too: we need to create a `shaderProgram`, then link the vertex and fragment shaders into it. 
As usual, create an int here. 
```
unsigned int shaderProgram; 
shaderProgram = glCreateProgram(); // no parameters

// then attach both shaders
glAttachShader(shaderProgram, vertexShader); 
glAttachShader(shaderProgram, fragmentShader);

// now they are attached, we link them and the program
glLinkeProgram(shaderProgram); 
// This is used to create an executable  that will run on the programmable vertex processor. 

// Now that the shaders are linked, they are formed into the executables, then we can actually delete the shader objects that we made earlier
glDeleteShader(vertexShader); glDeleteShader(fragmentShader);
```

#### Using the Program
`glUseProgram` - installing a program object as part of current rendering state. 

```
glUseProgram(shaderProgram);
```


##### Terminating 
At the end, don't forget to do the whole : `glfwTerminate();`
then `return 0;`. 


### Drawing
Then in our loop, we can draw, we have to pass in the Primitive that we want to draw. 
`glDrawArrays`
We specify what primitives to render. 

Here we want a triangle so `GL_TRIANGLES`, however there is also: 
![[Pasted image 20240607140145.png]]

`glDrawArrays(GL_WHATEVER, 0, n)`
0 is the index of which vertex to start on, n is the number of vertices to draw. 

To note that we are hardcoding a lot of numbers here, that will need to be variable as we create moving and changing vertices. 

### Another way of Creating Shaders
from TheCherno himself: 
He uses `static` for internal linkage. 
The thing with both these two is that they are passing the source string, just in case, you didn't have them globally. 
```
static unsigned int CompileShader(unsigned int type, const std::string& source)
{ 
	unsigned int id = glCreateShader(GL_VERTEX_SHADER);
	// if you pass in string, then convert to const char*
	// const char* src = source.c_str()
	glShaderSource(id, 1, &src, nullptr);
	glCompileShader(id);

	int result;
	glGetShaderiv(id, GL_COMPILE_STATUS, &result);
	if(result == GL_FALSE)
	{ 
		int length; 
		glGetShaderiv(id, GL_INFO_LOG_LENGTH, &length);
		char* message = (char*) alloca(length * sizeof(char))
		glGetShaderInfoLog(id, length, &length, message);
		LOG(message);
	}

	return id;
}
```
Internal linkage here. 

```
static unsigned int CreateShader()
{ 
	unsigned int program = glCreateProgram();
	unsigned int vs = CompileShader(GL_VERTEX_SHADER, vertexShader);
	unsigned int fs = CompileShader(Gl_FRAGMENT_SHADER, fragmentShader);

	glAttachShader(program, vs); glAttachShader(program,fs);
	glLinkProgram(program);
	glValidateProgram(program);

	glDeleteShader(vs); glDeleteShader(fs);
}
```



#VAO #VBO #EBO

VBO - an array of data that's stored for every vertex

EBO - an array of data that describes how every element is constructed, how the vertices fit together

VAO - bundle these together (like a container of pointers) and allow you to utilize them easier.

VBO's are not just triangles, they can store multiple meshes, in a single VBO and set the correct indices for drawing. 

The idea is that there is a buffer in the GPU, that's just data, it's just numbers there. If you want to use this data as a vertex attribute in a shader, you can call this buffer a VBO, and bind it as such. If you want to use it as a vertex indices, you can call it an EBO, and bind it as such. 

Each VAO has its own "namespace" of attribute bindings. It will store the stride/address. 
The VAO gets the data, and feeds it to the vertex shader. It's the middle man of the vertex data, and the vertex shader. 

https://webglfundamentals.org/webgl/lessons/resources/webgl-state-diagram.html?exampleId=triangle#no-help

