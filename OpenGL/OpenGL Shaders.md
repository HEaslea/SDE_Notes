[[OpenGL VertexShader]]
[[OpenGL FragmentShader]]

Little programs that rest on the GPU that run for every vertex that we have. 
They just transform the input to the output, just like every program that we have written ever. 
Shaders are not really allowed to communicate with each other, the only communication they have is via their inputs and outputs. 

## GLSL
C-like, however, it's tailored for use with graphics and contains useful features for specifically vector and matrix manipulation. 

They always begin with a version declaration. 
Following is a list of input and output variables, and its uniforms.
Then the main function, which is every shader's entry point. 

Typical structure: 
```
#version version_number

in type in_variable_name; 
in type in_variable_name;

out type out_variable_name; 

uniform type uniform_name;

void main()
{ 
	// process input(s) and do some weird graphics stuff to it

	// output the processed stuff, to output variable
	out_variable_name = weird_stuff_we_processed;
}
```

When we talk about the vertex shader, each input variable is also known as Vertex Attribute. 
There is a max number of vertex attributes that we can have, depending on the hardware. 

OPENGL will guarantee 16 4-component vertex attribs are available. 
You can query `GL_MAX_VERTEX_ATTRIBS` if you want to get the max that we can pass. 

#### Types: 
There are the most basics, like in C: `int, float, double, uint, bool`. 
There is also, two container types that we use a lot `vectors` and `matrices`. 
They take the following form:
```
vecn // default vector of n floats

bvecn // n booleans

ivecn // n integers

uvecn // n unsigned integers

dvec // an double components
```

Most of the time we want the floats, so we will use `vecn`. 

GLSL will allow component access with `rgba` and `stpq` for texture coordinates as well. 

##### Swizzling 
Allowing for syntax such as: 
```
vec2 someVec; 
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.zyw;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy;
```

```
vec2 vect = vec2(0.5, 0.7);
vec4 result = vec4(vect, 0.0, 0.0);
vec4 otherResult = vec4(result.xyz, 1.0);
```

### ins And outs
Shaders are nice on their own, they are part of a whole and for that reason we need to have inputs and outputs individually, so that we can move stuff around. 

`in` `out` are for that purpose. 
We can get input and output with those variables, and wherever an output variable matches with an input variable of the next shader stage, they're passed along. 

The vertex and the fragment shader will differ a little bit however. 

The vertex shader needs to have some form of input. 
This input will differ to the fragment, in some ways right. 
The vertex shader will read and get its input straight from the vertex data. 
To define how that works, we need to specify that the input variables with location metadata so we can configure the vertex attributes on the CPU. 

This is the whole `layout (location = 0)`. 
We need this extra data so that we can link it with the vertex data. 


The other exception is that the fragment shader requires a `vec4` colour output variable, as they need to generate the final output colour. If this is left undefined, OpenGL will just create them as black and white. 

 We can even have the vertex shader decide the colour for the fragment shader. 
```
#version 330 core
layout (location = 0) in vec3 aPos; 

out vec4 vertexColour; 

void main()
{
	gl_Position = vec4(aPos, 1.0); we give vec3 to vec4's ctor
	vertexColour = vec4(0.5, 0.0, 0.0, 1.0); // output already
}
```

```
#version 330 core
out vec4 FragColour; 
in vec4 vertexColour;

void main()
{ 
	FragColour = vertexColour;
}
```

As `vertexColour` has the same type and the same name, they are "passed" on from shader to shader. 


### Uniform
They are another way to pass CPU data to the GPU. 
They are global, They are unique per shader, and can be accessed from any shader at any stage in the shader program. 
If you set their value, they will keep it until they are reset or updated. 
IDK what this means, but I guess it's like any other variable. 


```
#Fragment Shader
#version 330 core
out vec4 FragColour;

uniform vec4 ourColour;

void main()
{ 
	FragColour = ourColour;
}
```
This one is currently empty, we haven't added any data into it yet. 

Let's spice things up with the colours some more: 
```
float timeValue = glfwGetTime();

float greenValue = (sin(timeValue) / 2.0f) + 0.5f; 
int vertexColourLoation = glGetUniformLocation(shaderProgram, "ourColour");
glUseProgram(shaderProgram);
glUniform4f(vertexColourLocation, 0.0f, greenValue, 0.0f, 1.0f);
```

The basics, getting the time, which is running along the x axis. 
Then we use `sin(tV)` in order to range it from -1 to 1, then / 2.0f and + 0.5f to get the range to change, remember that we can even speed this up, by `(x * a)` where a is however we want to speed it up by, these are just functions, and we can shape them however we want. 
Just go on desmos graph to make it easier to see. 

Then we query for the location of `ourColour` uniform using `glGetUniformLocation(shaderProgram, "ourColour")`. 
If it returns -1, that means we couldn't find that particular name in the shader that we specified. 
This gives us a way to specify what we are passing into the shaders. 

Updating this uniform will **require us to use the program first.**

Every frame: 
```
float timeValue = glfwGetTime();
float greenValue = sin(timeValue) / 2.0 + 0.5f;

// now we have the location, this will be -1 if we 
// can't get the location
int vertexColourLocation = glGetUniformLocation(program, "ourColour");

// then we set what the uniform data is
glUniform4f(vertexColourLocation, 0.0f, greenValue, 0.0f, 1.0f);

// then as usual
glClear(GL_COLOR_BUFFER_BIT);
glDrawArrays(GL_TRIANGLES, 0, 3);
```

So we get a colour out in terms of shaders now too. 

However, this is just to show that we can use uniforms in order to change the output overall. 

However, if we wanted to change each vertex distinctly, then we need to change the vertex attribute's data. 

YOU CAN EVEN WRITE YOURSELF YOUR OWN CLASS

Nothing special: 
```
class Shader
{ 
public: 
	unsigned int ID; 

	Shader(const char* vertexPath, const char* fragmentPath); // for path

	// use and activate shaders
	void use(); 

	// utility uniform functions
	void setBool(const std::string& name, bool value) const;
	void setInt(const std::string& name, int value) const;
	void setFloat(const std::string& name, float value) const;
};
```

Then if we want to read from a file in the constructor: 
```
Shader(const char* vertexPath, const char* fragmentPaht)
{ 
	// 1. retrieving the vertex/fragment source code from the file
	std::string vertexCode; 
	std::string fragmentCode; 
	std::ifstream vShaderFile;
	std::ifstream fShaderFile;

	// ensureing these ifstream objects can throw exceptions
	vShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
	fShaderFile.exceptions(std::ifstream::failbit | std::ifstream::badbit);
	
	try{ 
		vShaderFile.open(vertexPath);
		fShaderFile.open(vertexPath);
		std::stringstream vShaderStream, fShaderStream;
		// reading from buffer contents into stream
		vShaderStream << vShaderFile.rdbuf();
		fShaderStream << fShaderFile.rdbuf();

		vShaderFile.close();
		fShaderFile.close();

		vertexCode = vShaderStream.str();
		fragmentCode = fShaderStream.str();
	} catch (std::ifstream::failure e) { 
		std::cout << "ERROR::SAHDER::FILE_NOT_SUCCESSFULLY_READ" << std::endl;
	}
	const char* vShaderCode = vertexCode.c_str();
	const char* fShaderCode = fragmentCode.c_str();


	// 2. now compile
	unsigned int vertex, fragment; 
	int success; 
	char infoLog[512];

	vertex = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertex, 1, &vShaderCode, NULL);
	glCompileShader(vertex);

	glGetShaderiv(vertex, GL_COMPILE_STATUS, &success);
	if(!success)
	{ 
		glGetShaderInfoLog(vertex, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
	}

	// same for fragment Shader
	fragment = glCreaterShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragment, 1, &fShaderCode, NULL);
	glCompileShader(fragment);

	glGetShaderiv(fragment, GL_COMPILE_STATUS, &success);
	if(!success)
	{ 
		glGetShaderInfoLog(fragment, 512, NULL, infoLog);
		std::cout << "ERROR:SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
	}

	ID = glCreateProgram(); 
	glAttachShader(ID, vertex);
	glAttachShader(ID, fragment);
	glLinkProgram(ID);

	glGetProgramiv(ID, GL_LINK_STATUS, &success)
	{ 
		glGetProgramInfoLog(ID, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n" << infoLog << std::endl;
	}
	
	glDeleteShader(vertex);
	glDeleteShader(fragment);
}

//use is straightforward
void use()
{ 
	glUseProgram(ID);
}

void setBool(const std::string& name, bool value) const { 
	glUniformli(glGetUniformLocation(ID, name.c_str()), int()value);
}

void setInt(const std::string& name, int value) const 
{ 
	glUniformli(glGetUniformLocation(ID, name.c_str()), value);
}

void setFloat(const std::string& name, float value) const 
{ 
	glUniform1f(glGetUniformLocation(ID, name.c_str()), value);
}
```

USAGE: 
```
Shader ourShader("path to vertex shader.vs", "path to fragment shader.fs");
while(...)
{ 
	ourShader.use();
	ourShader.setFloat("someUniform", 1.0f);
	DrawStuff();
}
```
