Shaders do look slightly different: 
Something to note that had me confused for a long time was that before defining VAO attributes, we have to bind the right VBO that we are referring to, IDK why but hell yeah. 

The idea here, for me at least is that the vertexshader works with positions, and how we can create triangles if we need, however, in the vertex shader we have to let the fragment shader know that we are going to pass something to it. 

```
#define LOG(expr) myFile << expr << std::endl

const char* vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"layout (location = 1) in vec3 aColour;\n"
"out vec3 colourOut;\n"
"void main()\n"
"{\n"
"	gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"	colourOut = aColour;"
"}\0";

const char* fragShaderSource = "#version 330 core\n"
"in vec3 colourOut;\n"
"out vec4 FragColour;\n"
"void main()\n"
"{\n"
"	FragColour = vec4(colourOut, 1.0f);\n"
"}\0";

static unsigned int createShaderProgram() // it is what is is
{
	unsigned int program = glCreateProgram();
	unsigned int vs = glCreateShader(GL_VERTEX_SHADER); 
	unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(vs, 1, &vertexShaderSource, nullptr);
	glShaderSource(fs, 1, &fragShaderSource, nullptr);
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
		LOG(infoLog);
		std::cout << "Program Linking Fail" << infoLog;
	}

	glDeleteShader(vs); glDeleteShader(fs);

	return program;
}


int main(int argc, char* args[]) {
	glfwInit();
	GLFWwindow* window = glfwCreateWindow(500, 500, "Tri", NULL, NULL);

	if (window == NULL)
	{
		glfwTerminate();
		return -1;
	}

	glfwMakeContextCurrent(window);

	if (glewInit() != GLEW_OK)
	{
		std::string n("GLEW INIT FAILED");
		std::cout << n << std::endl;
		LOG(n);
	}
	
	unsigned int program = createShaderProgram();

	float posits[] = {
		-0.5f, -0.5f, 0.0f, 
		 0.5f, -0.5f, 0.0f,     
		 0.0f,  0.5f, 0.0f,	    
	};
	float colours[] = {
		 1.0f, 0.0f, 0.0f,
	     0.0f, 1.0f, 0.0f,
		 0.0f, 0.0f, 1.0f
	};

	unsigned int VAO; 
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);

	// positions buffer
	unsigned int positBuff; 
	glGenBuffers(1, &positBuff);
	glBindBuffer(GL_ARRAY_BUFFER, positBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(posits), posits, GL_STATIC_DRAW);


	// colour buffer
	unsigned int colourBuff;
	glGenBuffers(1, &colourBuff); 
	glBindBuffer(GL_ARRAY_BUFFER, colourBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(colours), colours, GL_STATIC_DRAW);


	// positions VAO 
	// you coudl do this when we have the VBO bound the first time
	glBindBuffer(GL_ARRAY_BUFFER, positBuff);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);


	// colours VAO
	glBindBuffer(GL_ARRAY_BUFFER, colourBuff);
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(1);


	glClearColor(0.0f, 0.0f, 0.0f, 1.0f);

	glUseProgram(program);
	
	while (!glfwWindowShouldClose(window))
	{
		// input 


		// render
		glClear(GL_COLOR_BUFFER_BIT);
		glDrawArrays(GL_TRIANGLES, 0, 3); // render to the back buffer

		glfwSwapBuffers(window);
		glfwPollEvents();
		std::this_thread::sleep_for(std::chrono::milliseconds(100));
	}


	glfwTerminate();
	LOG("CLOSING");
	return 0;
	
```




#### Same Triangle With Colours Rotating
Shaders are the same and omitted;
```
static unsigned int createShaderProgram()
{
	unsigned int program = glCreateProgram();
	unsigned int vs = glCreateShader(GL_VERTEX_SHADER); 
	unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(vs, 1, &vertexShaderSource, nullptr);
	glShaderSource(fs, 1, &fragShaderSource, nullptr);
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
		LOG(infoLog);
		std::cout << "Program Linking Fail" << infoLog;
	}

	glDeleteShader(vs); glDeleteShader(fs);

	return program;
}

void updateColours(float* colours, float elapsedTime)
{
	static int index = 0; 
	std::cout << index << std::endl;
	// this is written dreadfully for clarity
	switch (index)
	{
	case 0: colours[0] = 1.0f; colours[1] = 0.0f; colours[2] = 0.0f;
		colours[3] = 0.0f; colours[4] = 1.0f; colours[5] = 0.0f;
		colours[6] = 0.0f; colours[7] = 0.0f; colours[8] = 1.0f;
		break;

	case 1: colours[0] = 0.0f; colours[1] = 1.0f; colours[2] = 0.0f;
		colours[3] = 0.0f; colours[4] = 0.0f; colours[5] = 1.0f;
		colours[6] = 1.0f; colours[7] = 0.0f; colours[8] = 0.0f;
		break;
		
	case 2: colours[0] = 0.0f; colours[1] = 0.0f; colours[2] = 1.0f; 
		colours[3] = 1.0f; colours[4] = 0.0f; colours[5] = 0.0f;
		colours[6] = 0.0f; colours[7] = 1.0f; colours[8] = 0.0f;
		break;
	}

	index++;
	if (index == 3) index = 0;
}


int main(int argc, char* args[]) {
	glfwInit();
	GLFWwindow* window = glfwCreateWindow(500, 500, "Tri", NULL, NULL);

	if (window == NULL)
	{
		glfwTerminate();
		return -1;
	}


	glfwMakeContextCurrent(window);

	if (glewInit() != GLEW_OK)
	{
		std::string n("GLEW INIT FAILED");
		std::cout << n << std::endl;
		LOG(n);
	}
	
	unsigned int program = createShaderProgram();

	float posits[] = {
		-0.5f, -0.5f, 0.0f, 
		 0.5f, -0.5f, 0.0f,     
		 0.0f,  0.5f, 0.0f,	    
	};
	float colours[] = {
		 1.0f, 0.0f, 0.0f,
	     0.0f, 1.0f, 0.0f,
		 0.0f, 0.0f, 1.0f
	};

	unsigned int VAO; 
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);

	// positions buffer
	unsigned int positBuff; 
	glGenBuffers(1, &positBuff);
	glBindBuffer(GL_ARRAY_BUFFER, positBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(posits), posits, GL_STATIC_DRAW);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	// colour buffer
	unsigned int colourBuff;
	glGenBuffers(1, &colourBuff); 
	glBindBuffer(GL_ARRAY_BUFFER, colourBuff);
	glBufferData(GL_ARRAY_BUFFER, sizeof(colours), colours, GL_STATIC_DRAW);
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(1);



	glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
	glUseProgram(program);

	float startTime = glfwGetTime();

	while (!glfwWindowShouldClose(window))
	{
		// input 
		// calculate time for updating colours
		float currentTime = glfwGetTime();
		float elapsedTime = currentTime - startTime;

		// updating colours
		updateColours(colours, elapsedTime);
		glBindBuffer(GL_ARRAY_BUFFER, colourBuff);
		glBufferData(GL_ARRAY_BUFFER, sizeof(colours), colours, GL_STATIC_DRAW);



		// render
		glClear(GL_COLOR_BUFFER_BIT);
		glDrawArrays(GL_TRIANGLES, 0, 3); // render to the back buffer

		glfwSwapBuffers(window);
		glfwPollEvents();
		std::this_thread::sleep_for(std::chrono::milliseconds(1000));
	}


	glfwTerminate();
	LOG("CLOSING");
```


Remember that the Attrib Pointer is guiding something to that of the components that that attrib pointer represents, so we can only get those components. 
One thing you will notice about the rainbow triangle is that it doesn't have antialiasing, it's just diagonal as diagonal can be. 

Fragment Interpolation: the result of the blending of the colours in the triangle, colours we haven't supplied, but they are there. 

