GL Mathematics, using vectors and matrices etc. 

Here's where we put all our maths into practice, it's worth revisiting some of the maths in the amazing book that we have. 

Possibly even go ahead and write some of your own, it's interesting. 

There is a fantastic section in the OpenGL book as well. 

The main thing is getting GLM, the mathematics library into the system. 

Then you can imagine everything after that is just easy: 

You can create matrices for translation and the library basically takes care of the rest. 

Creating a vec if you want, with homogenous coordinate to show it's a point: 
`glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f);`

Creating a matrix that we will be using for transformation: 
`glm::mat4 mat = glm::mat4(1.0f);`
This 1.0f is to make sure that it is an identity matrix, if 1 put 0 in there, then it would be a null matrix: 


Then we need to work some multiplications on it. 
Remember that the output here will be taking every pos attribute in the mesh vertex list, and then multiplying it by the transform matrix. 
Remember too that the shader will be working on every vertex. 

Here is some code: 
```
glm::mat4 trans = glm::mat4(1.0f);
trans = glm::translate(trans, glm::vec3(0.0f, 1.0f, 0.0f)); 
// here we are translating in the y axis

// then if we had something like this: 
glm::vec4 vec(1.0f, 0.0f, 0.0f, 1.0f); // position vector

vec = trans*vec; // the vec will be transformed +1 y, within that space.

// we can also scale and transform

trans = glm::translate(trans, glm::radians(90.0f), glm::vec3(0.0, 0.0, 1.0));
// the translate here, trans here is creating a translation matrix

trns = glm::scale(trans, glm::ve3(0.5, 0.5, 0.5));
```

##### Uniforms and This
Remember that everything in OpenGL has a unsigned int that is the ID. 

We can get all the translation matrices together into one: 
```
auto finalMat = scale * rotate * translate;
```

```
#version 330 core
layout (location = 0) in vec3 aPos;

uniform mat4 transform;  // the key one

void main()
{ 
	gl_Position = transform * vec4(aPos, 1.0f);
}
```


Then just access this: 
```
unsigned int transformLoc = glGetUniformLocation(ourShader.ID, "transform");
glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(trans));
```
The first argument being the name, then how many we are sending, then we are asked if we want to transpose our matrix, no in this case, but you might want to do it for funny-ness. 

![[Pasted image 20240627122607.png]]
Then we have to pass the actual matrix data. 
GLM does store matrices in a way that OpenGL doesn't always expect, making it easier for GLM, but you do have to convert, just in case, using the built-in function `value_ptr`. 


```
#include <GL/glew.h>
#include <GLFW/glfw3.h>

#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>


#include <iostream>
#include <fstream>
#include <string>
#include <set>
#include <cmath>
#include <random>
#include <iomanip>
#include <thread>
#include <sstream>
#include <ctime>
#include <vector>
#include <stdio.h>
#include <stdlib.h>
#include <algorithm>
#include <chrono>

class Shader {
public: 
	unsigned int ID; 
	Shader(const char* vertexPath, const char* fragmentPath);

	void use();  

	void setBool(const std::string& name, bool value) const; 
	void setINt(const std::string& name, int value) const; // wtf is going on here? 

};




const char* vShader = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"uniform mat4 transform;\n"
"void main()\n"
"{\n"
"	gl_Position = transform * vec4(aPos, 1.0f);\n"
"}\n";

const char* fShader = "#version 330 core\n"
"out vec4 FragColour;\n"
"uniform vec4 ourColour;\n"
"void main()\n"
"{\n"
"	FragColour = ourColour;\n"
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
	GLFWwindow* window = glfwCreateWindow(1000, 1000, "Test", NULL, NULL);

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
		-0.5f, -0.5f, 0.0f,
		 0.5f, -0.5f, 0.0f,
		 0.0f,  0.5f, 0.0f,
	};

	unsigned int VAO; 
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);

10
	unsigned int positBuff; 
	glGenBuffers(1, &positBuff);
	glBindBuffer(GL_ARRAY_BUFFER, positBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(posits), posits, GL_STATIC_DRAW);

	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	glClearColor(0.0f, 0.0f, 0.0f, 1.0f);

	glUseProgram(program);

	// creating a transform matrix
	glm::mat4 trans;
	glm::mat4 scale = glm::mat4(1.0f); // identity 4 matrix
	glm::mat4 rot = glm::mat4(1.0f);
	
	float sf;

	long iteration = 0;
	while (!glfwWindowShouldClose(window))
	{
		// input 
		
		sf = (sin(glfwGetTime() * 5) / 20.0f) + 1.0f;
		scale = glm::scale(scale, glm::vec3(sf, sf, sf)); // more of these /s
		rot = glm::rotate(rot, glm::radians(5.0f), glm::vec3(0.0f, 1.0f, 1.0f));
		trans = scale * rot;




		// render
		
		int vertexColourLocation = glGetUniformLocation(program, "ourColour");
		glUniform4f(vertexColourLocation, 0, 1.0f, 0, 1.0f);

		int transformLoc = glGetUniformLocation(program, "transform");
		glUniformMatrix4fv(transformLoc, 1, GL_FALSE, glm::value_ptr(trans));


		glClear(GL_COLOR_BUFFER_BIT);
		glDrawArrays(GL_TRIANGLES, 0, 3);
	
		printf("ITERATION : %i\n", iteration++);


		glfwSwapBuffers(window);
		glfwPollEvents();
		std::this_thread::sleep_for(std::chrono::milliseconds(10));
	}


	glfwTerminate();
	return 0;
}
```


We are about ready to go 3D. 



