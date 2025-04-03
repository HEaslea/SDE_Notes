What OpenGL expects is that every vertex is within the normalized device coordinates after each vertex shader run. Therefore they should be [-1.0, 1.0]. 
This is the idea when we want to create our own 3d things in python etc. we use this coordinate space in order to create simple objects, rather than having to convert and change spaces etc. 

![[Pasted image 20240627123351.png]]

![[Pasted image 20240627123428.png]]
Clipping space will get rid of those coordinates that do not appear in the right area, they are clipped out. 

Projection to clip space coordinate can add perspective if using perspective projection. 

Then the screen space will transfer clip space coordinates into screen space coordinates. 

The resulting coordinates are then sent to the rasterizer to turn them into fragments. 

All of this has to happen before we rasterize. 

Remember that these matrices used for multiplication are just changing the the numbers so the points are just relating to a new origin. 

Take the model matrix, that is from object space into world space, it translates, scales and/or rotates your object to place it in the world at the location/orientation that they belong. 

View space is just the camera. 

At the end of each vertex shader run, OpenGL expects the coordinates to be within a specific range and any coordinate that falls outside this range is clipped. 
They are discarded. 

from view to clip space we use a so called projection matrix that specifies a range of coordinates eg. -1000 1000 in each dimension. 
We first then transform coordinates here to normalize them (-1.0, 1.0). 
All coordinates outside of this will not be transformed and not be clipped. 
so here a coordinate of (1250, 500, 750) will not be visible. 

> Note that if part of a primitive, eg. a triangle is outside the clipping volume, then OpenGL will reconstruct the triangle as one or more triangles to fit inside the clipping range. 

This viewing box is known as the frustum. 
Each coordinate in this will end up on the screen some how. 

![[Pasted image 20240627125153.png]]
Then we perform a perspective division, where we divide the x, y and z by w. 
Transforming the 4d clip space coordinates to 3D normalized device coordinates. 

Then we have to map these coordinates to the screen. 
Using the setting of `glViewport`. 

### Orthographic Projection
![[Pasted image 20240627130538.png]]
Defining a cube-like frustum, defining the clipping space. 
We specify the width, height and length of the visible frustum. 

The frustum will be to define the visible coordinates and is specified by a width, a height and a near and far plane. 

This frustum will directly map all coordinates inside the frustum to normalized device coordinates without any special side effects, it won't touch the w component of the transformed vector. 

Creating one: 
`glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);`

There is left and right coordinate for first two args, then top and bottom. With those we have defined the dimension of the near and far planes. Then the 5th and 6th will define the distances between those planes. 

This type of projection directly maps coordinates to the 2d plane that is your screen. We are not taking perspective into account here. Something that perspective projection will make a lot easier for us. 

## Perspective Projection
It's obvious, but the further away objects are, the smaller they look, so how do we do that in maths. 

Here we have to use a perspective projection matrix. 
There is a manipulation where we are mapping to a clip space **and** changing the w so that the further away something is, the larger the w is. When everything is transformed to clip space they are in the range -w to w (anything outside this range is clipped). once the coordinates are in clip space, perspective division takes place: 

	![[Pasted image 20240627185418.png]]


Therefore we get smaller coordinates the further away we get from the viewer. 
This is another reason for the w, actually pretty useful, not only for distinction between a direction and a point too, oh and the extra row gives us the translation ("column" in some circumstances). 

Now, after that the coordinates are in NDC. 

Here is a funny thing: 
[A funny thing](https://www.songho.ca/opengl/gl_projectionmatrix.html)

Everything that we have written so far has been in NDC space. Which is what they have to be in, for openGL to do anything. 

How to make: 
`glm::perspective(glm::radians(45.0f), (float)width / (float)height, 0.1f, 100.0f);`
Here we are creating a large frustum, defining the visible space, anything outside gets clipped. 
![[Pasted image 20240627190251.png]]

The parameters; 1. fov, realistic is usually 45, the higher, the less zoomed out it will look, thinking about the idea of the same number of pixels for more things to be seen, 2. the aspect ratio (width/height) 3. & 4. setting the near and far plane. 

Here's a thing too, when you set the near plane too far away, none of those coordinates in that space will be rendered, therefore, you can see right through them, which is something that sometimes happens in video games. 



#### All Together Now
Remembering that we need to read matrix multiplication from right to left. 
The resulting vertex should then be assigned to `gl_Position` in the vertex shader.


### GOING 3D
To start, we need a model matrix. 
Consisting of translations, scaling and/or rotations we'd like to apply to `transform` all object's vertices to the global world space. 

In this case let's rotate our model a little bit from its space to the world space, by rotating about the x axis, making it look like it's lying on the floor. 

Let's take the triangle, or the plane that we have in our code at the moment. 


In order to get a vector that is within Clip Space, which is what our vertex shader is needed. 
Then we do something like this: 
![[Pasted image 20240701173902.png]]

The resulting vertex should then be assigned to `gl_Position`, in the vertex shader, openGL will perform perspective division and clipping automatically. 
The vertex shader will get the coordinates into NDC. 
OpenGL will then use the parameters of the `glViewPort` to map the NDC to screen coordinates. 
This is known as `viewport transform`. 

### Going 3D
In order to start, we obviously need a model matrix. 
This will consist of translations, scaling and/or rotations we'd like to apply to `transform` all object's vertices to the global world space. Say we want to rotate the plant that we have been using so far, by rotating it on the x-axis, so that it looks more like its laying on the floor. 

```
glm::mat4 model = glm::mat4(1.0f);
model = glm::rotate(model, glm::radians(-55.0f), 
			glm::vec3(1.0f, 0.0f, 0.0f));
```
By multiplying our vertex coordinates, with this model, we are transferring model coordinates to world coordinates. Our plane that is slightly on the floor, represents the plane that is in the world space. 
Then we create a view matrix, for the camera. Maybe we want to move slightly backwards from the plane, so the object becomes visible, as in world space we were right on top of it, as the origin was still (0,0,0);

Think about the thing in the math book, when we move objects, we can either move the whole scene, or the camera, either is fine, the result is the same. 

As we want to move backwards, as openGL is a right handed system. We want to move in the z+ axis, therefore everything else needs to move in the -z axis. 

![[Pasted image 20240701175343.png]]

Remember this, the positive x is usually to the right, +y is "up". 
Think of the centre, the origin, particularly in view space, as being in the centre of your screen. 
Your thumb is the +x axis if you use the right hand pointing idea. 

OpenGL will switch which hand we use when it comes to NDC, which is fucking annoying. 

All we need for the view matrix: 
```
glm::mat4 view = glm::mat4(1.0f);
view = glm::translate(view, glm::vec3(0.0f, 0.0f, -3.0f));
```
The last thing that we need to define is the projection matrix: 
We want to use perspective projection here: 
```
glm::mat4 projection; 
projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);
```

Remember that we have our aspect ratio, our FOV, our near and far planes in here, very cool indeed. 

Now that we have created our transformation matrices, we should then pass them onto our shaders. 
We should have them as uniforms in there: 
```
#version 330 core
layout (location = 0) in vec3 aPos;
... 
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{ 
	// note that we read the mulitplication from right to left
	gl_Postion = projection * view * model * vec4(aPos, 1.0f);
	...
}
```

Transformation Matrices change all the time, therefore we need to make sure that they change in our code as we go along: 
```
int modelLoc = glGetUniformLocation(ourShader.ID, "model");
glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));
... 
```
Then the same for View Matrix and Projection Matrix

So I have my cube in the code below, however, I also want to add the Z-buffer , so that we aren't just drawing triangles on top of each other as they are worked out. 

GLFW is really nice and makes one for us. 
The depth is stored with each fragment, and whenever that fragment wants to output its color, It will compare its depth with the z_buffer. 
Depth Testing done automatically in openGL. 

It is disabled by default in openGL too. 

We can use `glEnable`. 
This will allow us to enable and disable certain things in our state machine. 
All we need to do is
`glEnable(GL_DEPTH_TEST);`

Since we are using a depth buffer, we want to clear it, same as the colour buffer, every frame. 
Otherwise the previous frame's information will be still in the buffer. 

Therefore we do: 
`glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);`


```



const char* vShader = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"layout (location = 1) in vec3 aColor;\n"
"uniform mat4 model;\n"
"uniform mat4 view;\n"
"uniform mat4 projection;\n"
"out vec3 ourColor;\n"
"void main()\n"
"{\n"
"	gl_Position = projection * view * model * vec4(aPos, 1.0);\n"
"   ourColor = aColor;"
"}\n";

const char* fShader = "#version 330 core\n"
"out vec4 FragColor;\n"
"in vec3 ourColor;\n"
"void main()\n"
"{\n"
"	FragColor = vec4(ourColor, 1.0f);\n"
"}\n";

static unsigned int createShaderProgram() // it is what is is
{
	unsigned int program = glCreateProgram();
	unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
	unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(vs, 1, &vShader, nullptr);
	glShaderSource(fs, 1, &fShader, nullptr);
	glCompileShader(vs); glCompileShader(fs);

	int success;
	char infoLog[512];
	glGetShaderiv(vs, GL_COMPILE_STATUS, &success);
	if (!success)
	{
		glGetShaderInfoLog(vs, 512, NULL, infoLog);
		std::cout << "Vertex Shader Error : " << infoLog << std::endl;
	}
	glGetShaderiv(fs, GL_COMPILE_STATUS, &success);

	if (!success)
	{
		glGetShaderInfoLog(vs, 512, NULL, infoLog);
		std::cout << "Fragment Shader Error : " << infoLog << std::endl;
	}

	glAttachShader(program, vs); glAttachShader(program, fs);

	glLinkProgram(program);

	glGetProgramiv(program, GL_LINK_STATUS, &success);
	if (!success)
	{
		glGetProgramInfoLog(program, 512, NULL, infoLog);
		std::cout << "Program Linking Fail" << infoLog;
	}

	glDeleteShader(vs); glDeleteShader(fs);

	return program;
}

void framebuffer_size_callback(GLFWwindow* window, int w, int h)
{
	printf("RESIZED : WIDTH: %i     HEIGHT: %i\n", w, h);
	glViewport(0, 0, w, h);
}

int main(int argc, char* args[]) {
	glfwInit(); 
	GLFWwindow* window = glfwCreateWindow(800, 600, "Test", NULL, NULL);

	if (window == NULL)
	{
		glfwTerminate(); 
		return -1;
	}
	glfwMakeContextCurrent(window);

	if (glewInit() != GLEW_OK)
	{
		std::cout << "GLEW FAIL" << std::endl;
		glfwTerminate(); 
		return -1;
	}

	unsigned int program = createShaderProgram();

	glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

	float posits[] = {
		-0.5f, -0.5f, -0.5f,	1.0f, 0.0f, 0.0f,
		0.5f, -0.5f, -0.5f,		1.0f, 0.0f, 0.0f,
		0.5f, 0.5f, -0.5f,		1.0f, 0.0f, 0.0f,
		0.5f, 0.5f, -0.5f,		1.0f, 0.0f, 0.0f,
		-0.5f, 0.5f, -0.5f,		1.0f, 0.0f, 0.0f,
		-0.5f, -0.5f, -0.5f,	1.0f, 0.0f, 0.0f,

		-0.5f, -0.5f, 0.5f,		0.0f, 1.0f, 0.0f,
		0.5f, -0.5f, 0.5f,		0.0f, 1.0f, 0.0f,
		0.5f, 0.5f, 0.5f,		0.0f, 1.0f, 0.0f,
		0.5f, 0.5f, 0.5f,		0.0f, 1.0f, 0.0f,
		-0.5f, 0.5f, 0.5f,		0.0f, 1.0f, 0.0f,
		-0.5f, -0.5f, 0.5f,		0.0f, 1.0f, 0.0f,

		-0.5f, 0.5f, 0.5f,		0.0f, 0.0f, 1.0f,
		-0.5f, 0.5f, -0.5f,		0.0f, 0.0f, 1.0f,
		-0.5f, -0.5f, -0.5f, 	0.0f, 0.0f, 1.0f,
		-0.5f, -0.5f, -0.5f,	0.0f, 0.0f, 1.0f,
		-0.5f, -0.5f, 0.5f,		0.0f, 0.0f, 1.0f,
		-0.5f, 0.5f, 0.5f, 		0.0f, 0.0f, 1.0f,

		0.5f, 0.5f, 0.5f,		0.0f, 1.0f, 1.0f,		
		0.5f, 0.5f, -0.5f, 		0.0f, 1.0f, 1.0f,
		0.5f, -0.5f, -0.5f,		0.0f, 1.0f, 1.0f,
		0.5f, -0.5f, -0.5f,		0.0f, 1.0f, 1.0f,
		0.5f, -0.5f, 0.5f, 		0.0f, 1.0f, 1.0f,
		0.5f, 0.5f, 0.5f,		0.0f, 1.0f, 1.0f,

		-0.5f, -0.5f, -0.5f,	1.0f, 1.0f, 0.0f,
		0.5f, -0.5f, -0.5f, 	1.0f, 1.0f, 0.0f,
		0.5f, -0.5f, 0.5f, 		1.0f, 1.0f, 0.0f,
		0.5f, -0.5f, 0.5f, 		1.0f, 1.0f, 0.0f,
		-0.5f, -0.5f, 0.5f, 	1.0f, 1.0f, 0.0f,
		-0.5f, -0.5f, -0.5f,	1.0f, 1.0f, 0.0f,

		-0.5f, 0.5f, -0.5f,		1.0f, 0.0f, 1.0f,
		0.5f, 0.5f, -0.5f, 		1.0f, 0.0f, 1.0f,
		0.5f, 0.5f, 0.5f, 		1.0f, 0.0f, 1.0f,
		0.5f, 0.5f, 0.5f, 		1.0f, 0.0f, 1.0f,
		-0.5f, 0.5f, 0.5f, 		1.0f, 0.0f, 1.0f,
		-0.5f, 0.5f, -0.5f,		1.0f, 0.0f, 1.0f
	};



	unsigned int VAO; 
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);


	unsigned int positBuff; 
	glGenBuffers(1, &positBuff);
	glBindBuffer(GL_ARRAY_BUFFER, positBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(posits), posits, GL_STATIC_DRAW);


	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
	glEnableVertexAttribArray(1);

	glClearColor(0.0f, 0.0f, 0.0f, 1.0f);

	glUseProgram(program);

	// model matrix
	glm::mat4 model = glm::mat4(1.0f);
	model = glm::rotate(model, glm::radians(-35.0f), glm::vec3(1.0f, 0.0f, 0.0f));

	// view matrix
	glm::mat4 view = glm::mat4(1.0f);
	view = glm::translate(view, glm::vec3(0.0f, 0.0f, -5.0f));

	// projection matrix
	glm::mat4 projection; 
	projection = glm::perspective(glm::radians(45.0f), 800.0f / 600.0f, 0.1f, 100.0f);


	int viewLoc = glGetUniformLocation(program, "view");
	glUniformMatrix4fv(viewLoc, 1, GL_FALSE, glm::value_ptr(view));

	int projLoc = glGetUniformLocation(program, "projection");
	glUniformMatrix4fv(projLoc, 1, GL_FALSE, glm::value_ptr(projection));

	glEnable(GL_DEPTH_TEST);

	long iteration = 0;
	while (!glfwWindowShouldClose(window))
	{
		// input 


		// other

		model = glm::rotate(model, glm::radians(2.0f), glm::vec3(0.5f, 1.0f, 0.4f));

		int modelLoc = glGetUniformLocation(program, "model");
		glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));

		// render
		

		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
		glDrawArrays(GL_TRIANGLES, 0, 36);
	
		printf("ITERATION : %i\n", iteration++);


		glfwSwapBuffers(window);
		glfwPollEvents();
		std::this_thread::sleep_for(std::chrono::milliseconds(10));
	}


	glfwTerminate();
	return 0;
}
```

Here is my spinning cube :)

Then you can get two cubes, by changing the model matrix, meaning that we use the same cube points, just that the model moves it out further. 

Remember all the way through, that this is not a "real world" but rather just a mathematical model of a world, therefore, all these rotations are very simple, they are not too costly, and the way that you imagine the world, ie, if we want to walk around a door and see it at different angles, then we have to move, but here, we can just rotate the door. 



[From OpenGLStackExchange](https://gamedev.stackexchange.com/questions/40741/why-do-we-move-the-world-instead-of-the-camera#:~:text=The%20camera%20in%20OpenGL%20cannot,to%20construct%20the%20appropriate%20view.)


**Understanding world space and view space**

To code this kind of behavior, you'll render the contents of the 3D world from the camera's point of view, not just from the world coordinate system point of view, or from some other fixed point of view.

Generally speaking, a 3D scene contains a set of 3D models. The models are defined as a set of vertices and triangles, referenced to their own coordinate system. The space in which the models are defined is called the model (or local) space.

After placing the model objects into a 3D scene, you'll transform these models' vertices using a "world transform" matrix. Each object has its own world matrix that defines where the object is in the world and how it is oriented.

This new reference system is called "world space” (or global space). A simple way to manage it is by associating a world transform matrix to each object.

In order to implement the behavior of a 3D camera, you'll need to perform additional steps. You'll reference the world—not to the world origin—but to the reference system of the 3D camera itself.

A good strategy involves treating the camera as an actual 3D object in the 3D world. Like any other 3D object, you use a "world transform" matrix to place the camera at the desired position and orientation in the 3D world. This camera world transform matrix transforms the camera object from the original, looking forward rotation (along the z-axis), to the actual world (xc, yc, zc) position, and world rotation.

Following figure shows the relationships between the World (x, y, z) coordinate system and the View (camera) (x', y', z') coordinate system.

The LookAt matrix moves everything in the world, so that the camera is constantly looking down the -z axis. 

So the eye part of the LookAt matrix will will move everything, like moving the camera by moving everything else in the scene. 
Then the look direction will be a rotation, aligning the scene with the camera's view direction. 

So when we specify the view matrix target, we translate everything in the world to move around the camera so that we are pointing towards that target, without ever moving or rotating the camera. 
So if our camera is said to be at (0,0,5), then we move everything else by (0,0,-5);

In our simple use of MVP, we move everything by (0,0,-3), since we are already facing the right way. 

Our view matrix is often created using our LookAt configuration. 
In a sense we are moving our world origin about and around the camera. 

- **Model Transformation**: Transforms object vertices from local (object) space to world space.
    
    - **Model Matrix (M)**: Applies translations, rotations, and scaling to place the object correctly in the world.
- **View Transformation**: Transforms vertices from world space to camera (view) space.
    
    - **View Matrix (V)**: Positions and orients the world so that the camera appears at the origin and points in the correct direction.
    - **Look-at Matrix**: A common way to create the view matrix, using the camera position, target, and up vector to define the transformation.
    
**Projection Transformation**: Transforms vertices from camera space to clip space.

- **Projection Matrix (P)**: Defines the camera’s lens, determining how 3D points are projected onto the 2D screen.
- **Types of Projection**:
    - **Perspective Projection**: Mimics the way the human eye sees, with objects farther away appearing smaller.
    - **Orthographic Projection**: Projects objects without perspective distortion, keeping sizes uniform regardless of distance.

A very good video to explain things a little further 
[Good Video](https://www.youtube.com/watch?v=svKVqROlVSg)

The Depth in NDC is just for the depth test, it's essentially a 2 dimensional scene. 

[Another Video](https://www.youtube.com/watch?v=JcDko6EVQrI)

Another key thing is that we never change the basis vectors.


Ok So here is the thing that I'm figuring out, in order to go to our fragment shader, we have to be in NDC, which is what we get when we apply our projected matrix. 
Therefore in our vertex shader, we apply everything we need to, baring in mind, this might not be a model view projection etc. However as long as our coordinates are in NDC by the time that they are outed to our fragment shader, then we are all good. 
The projection matrix will do this for you, it iwll map everything to NDC. 
Once we go from camera to projected space (NDC space) the camera doesn't exist anymore, it's done it's job for POV to get into NDC space. 

[There is only one coordinate space](https://www.youtube.com/watch?v=uQNU5XICGvw)

When we do matrix multiplication, we can kind of see it as defining a new basis coordinates for out vector. 

![[Pasted image 20240713191410.png]]

So it seems that we are using our w after projection, in order to see what is to be clipped or not. 