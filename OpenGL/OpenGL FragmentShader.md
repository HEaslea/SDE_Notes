#openglfragmentshader
The second and final shader we're going to create, for rendering a triangle. 

This one, is of course, all about calculating the right colour of output for our pixels. 

Let's say here, that we just want to output the same "orange" colour that we had before. 

Colours are usually represented with RGBA, I don't know more about the colour format.

```
#version 330 core 
out vec4 FragColour; 

void main()
{ 
	FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```
It only requires one output variable and that is the vector of size 4, that defines the final colour output that we should calculate ourselves. 
We can declare output as `out`, here we have just named it `FragColour`. 

To compile it's the same as the [[OpenGL VertexShader]]. 
```
unsigned int fragmentShader; 
fragmentShader = glcreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmenShader);
```

Now the only thing that we have to do is link both shader projects into a **shader program** that we can use for rendering. 

Making sure we check for compile errors as we go. 

## Shader Program
The finalised, compilation of two linked version of multiple shaders. 
So what we have done, then we link them, and then activate this shader program when rendering objects. 

This will then be used when we issue render calls. 

```
unsigned int shaderProgram; 
shaderProgram = glCreateProgram();
```
`glCreateProgram()` will return the ID to the program that we just created. 
Then we attach. 
```
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram); 
```
We attach then link. 

We can also see if this was successful as well. 
Instead of using `glGetShaderiv` and `glGetShaderInfoLog`
We use : 
```
char infoLog[512];
unsigned int success;
glGetProgramiv(shaderProgam, GL_LINK_STATUS, &success)
if(!success){ 
	glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
	...
}
```

The result is that we ahve a program that we can use, by using `glUseProgram`. 

You can then even delete the shaders, as we have linked them into the program, and we don't need them anymore, all that work lol. 
```
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```
Now, in all, we should have sent some data to the GPU and told it how to use that data, using the vertex and fragment shader. 
However, the GPU still does not really know how to interpret what we have sent. 
[[OpenGL Linking Vertex Attributes]]