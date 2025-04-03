[How Shaders Work](https://www.youtube.com/watch?v=5W7JLgFCkwI&list=PLlrATfBNZ98foTJPJ_Ev03o2oq3-GGOS2&index=7)

How he does it, is creating a function, which seems to be an effective way of doing it compared to the book: 
```
static unsigned int compilerShader(unsigned int type, const std::string& source)
{ 
	unsigned int id = glCreateShader(type);
	const char* src = source.c_str(); // make sure source is still a thing
	// pointer to beginning ouf our data 
	// or &source[0];
	
	glShaderSource(id, 1, &src, nullptr);
	glCompileShader(id);

	// error handling, 
	int result;
	glGetShaderiv(id, GL_COMPILE_STATUS, &result);
	if(result == GL_FALSE)
	{ 
		int length; 
		glGetShaderiv(id, GL_INFO_LOG_LENGTH, &length);
		// allocating on the stack dynamically
		char message = (char*)alloca(lenth * sizeof(char)); 
		glGetShaderInfoLog(id, length, &length, message);
		std::cout << "Failed to Compile" << (type == GL_VERTEX_SHADER ? "vertex" : "fragment") << " shader!" << std::endl;
		std::cout << message << std::endl;
		glDeleteShader(id);
		return 0;
	}
	return id;
}

static int CreateShader(const std::string& vertexShader, const std::string& fragmentShader)
{ 
	// taking in the source code as strings
	// which is what it does in the book, you can download them from the internet, and from a file etc. 
	// if you write your own in a file and then open it and implace

	unsigned int program = glCreateProgram();
	unsigned int vs = CompileShader(GL_VERTEX_SHADER, vertexShader);
	unsigned in fs = CompileShader(GL_FRAGMENT_SHADER, fragmentShader);

	// then link to the program 
	glAttachShader(program, vs);
	glAttachSahder(program, fs);
	glLinkProgram(program);
	glValidateProgram(program);


	glDeleteShader(vs);
	glDeleteShader(fs);
}
```
When we have linked them, and validated our program, we want to delete the intermediary shaders, they are not necessary, we compile them, linked them, delete the intermediary file. 

Remember that strings like this `" " " "  " "` will be concatenated.
Writing our shader: 
```
std::string vertexShader = 
"#version 330 core\n"
"\n"
"layout(location = 0) in vec4 position;\n"
"" // blank line
"void main()\n"
"{\n"
"    gl_Position\n"
"}";
```

The 0 should be matching up the 0 in the vertex attributes. 

Then remember at the end to use `glDeleteProgram(myProgram);`. 
