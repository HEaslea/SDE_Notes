If we want to do any rendering, then we need to learn about the vertex and fragment shaders. 

#shaderlanguage
To write a shader, we need to write in shader language `GLSL` (OpenGL Shading Language), and then compile this shader so we can use it in our application. 

It is very similar to C, according to clever people, so for me, this is going to be fucking ridiculous. 

"Simple Version": 
```
#version 330 core
laytout (location = 0) in vec3 aPos;

void main() 
{ 
	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

Every shader must declare it's version.
If OpenGL is using 3.3, then our version will be 330, if 4.2, then 420 etc. etc. very simple. 

Then we have to declare all the input vertex attributes in the vertex shader with the `in` keyword. 

For now, let's only care about the position data. 
So we only need a single vertex attribute. 
GLSL has vector datatypes that contains 1 to 4 floats based on its postfix digit. 
Since each, is a 3d pos, we create a `vec3` input variable with the name `aPos`. 
Then we set the location of the input variable via `layout (location = 0)`. 
`gl_Position` is predefined, and is our output for the shader. 
This is a `vec4` behind the scenes, therefore we need to cast whatever we have to that. 
Here we are just setting `w` to `1.0f`. This is because they are positions, not directions. 

Literally all we have done here is just output what we have put in, we have done nothing with it. 

Now we have to compile: 
## Compiling a Shader
#compilingashader
We take the source code for the vertex shader and store it in a const C string at the top of the code file for now:
```
const char *vertexShaderSource = "#version 330 core\n" "layout (location = 0) in vec3 aPos;\n" "void main()\n" "{\n" 
" gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n" "}\0";
```

In order to use it, OpenGL will have to dynamically compile it at run-time from its source code. 
Therefore we have to create a shader object, again reference by an ID. 
```
unsigned int vertexShader; 
vertexShader = glCreateShader(GL_VERTEX_SHADER);
```

Then we need to attach the source that we have written: 

```
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);
```
The `1` relates to how many strings we are passing as source code. 


Then error checking is a little odd, however, it will look something like this: 
```
int success; 
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
```
Then we check the success: 
```
if(!success)
{ 
	glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
	cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << endl;
}
```

Writing our own cout debug of vertex shading compilation, fairly simple. 


For every vertex, the vertex shader will be called. Telling us where that vertex will be on our screen. 

Same with the fragment shader, it will need to be run for every pixel within that rasterization. It decides which colour that pixel is supposed to be. 