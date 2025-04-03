## ASSIMP
We can't implicitly define all the vertices of complex shapes. 
Import these models from something like blender etc. 

Then, it becomes part of our job that we have to parse all the details that are necessary to us. 

There are simpler formats than others. eg. Wavefront.obj 

[DataFormatting](https://en.wikipedia.org/wiki/Wavefront_.obj_file)

Note that normals might not be normalized here. 

##### The Model Loading Library
*Open Asset Import Library*. 

The general idea being that ASSIMP has generalized data structures, that we load our data into, and then we just abstract our data from that data structure. 
The data structure will stay the same, even when the format we input is different. Meaning that our methods are always going to be the same, with varying input formats. 

![[Pasted image 20240805123940.png]]All scene data in `Scene`. 
The `Mesh` object is whawt we will predominantly want. 
A `Face` will represent the indices of the vertices that form a primitive eg. a Triangle etc.
Making it very easy for us to render using index buffers. 
Then we can also get the material properties too. 
First, we just want to load an object into a Scene object. 
Retrieve the corresponding Mesh objects from each of the nodes, Process each to get vertex data, indices and material properties if we want them. 

Then the result, we might want to abstract here, mostly, we just want the data in a `Model` object. 

Usually, as you know, we have multiple "objects", there are several sub-models/shapes that it consists of. 
Each of those single shapes is called a `mesh`. 
The `Model` will then consists of several meshes. 
Then to render a model, then we need to render each of the meshes, not concerning the model as a whole, but it's component parts. 


### Building ASSIMP
I used cmake in order to get SLL. 
In my file at the moment, I'm using the DLL, which was a pain in the ass. 
At this point, I think I got it working, and the files are in the harddrive. 


### Mesh
Loading meshes into ASSIMP, then taking the data to our own structures, that's what we want to do. 

Minimally, we need a: 
Vertex data for a mesh, a Position, Normal, TexCoords (however, we don't really need this last one).
```
struct Vertex { 
	glm::vec3 Position; 
	glm::vec2 Normal;
	glm::vec2 TexCoords;
};
```


If you want the texture data: 
```
struct Texture { 
	unsigned int id;
	string type; // eg diffuse or specular 
};
```

Then we can star the whole thing: 
```
class Mesh { 
public: 
	// mesh data
	std::vector<Vertex> vertices;
	std::vector<unsigned int> indices;
	std::vector<Texture> textures;

	Mesh(std::vector<Vertex> vertices ... )
	void Draw(shader& shader);
private: 
	unsigned int VAO, VBO, EBO;


	void setUpMesh();
};
```

Mesh Constructor is super simple: 
```
Mesh(vector vertices, vector indices, vector textures) { 
	this->vertices = vertices; 
	this->indices = indices; 
	this->textures = textures;  
	setupMesh(); 
}
```

Set up Mesh is super simple as well: 

```
void setupMesh() 
{ 
	glGenVertexArrays(1, &VAO);
	glGenBuffers(1, &VBO);
	glGenBuffers(1, &EBO);

	glBindVertexArray(VAO);
	
	glBindBuffer(GL_ARRAY_BUFFER, VBo);
	glBufferData(GL_ARRAYB_FFUER, vertices.size() * sizeof(Vertex), 
		&vertices[0], GL_STATIC_DRAW);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, indices.size() * sizeof(unsigned int),
		&indices[0], GL_STATIC_DRAW); 

	//positions
	glEnableVertexAttribArray(0);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*0));
	// don't normalize here

	//normals
	glEnableVertexAttribArray(1);
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, Normal));

	// tex coord
	glEnableVertexAttribArray(2);
	glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, TexCoords));

	glBindVertexArray(0);
}
```


A great property of structs, is that in memory they are sequential. 
![[Pasted image 20240805145828.png]]
Making them translate perfectly for what we are trying to do. 
Another thing we have not seen yet is the `offsetof(s, m)` `s` is the struct, and `m` is the variable. 

### Rendering
Read again about the texture section, that doesn't really concern me at this moment. 


Then when it comes to draw : 
```
void Draw(Shader &shader)
{ 
	// look at the book for textures here
	glBindVertexArray(VAO);
	glDrawElements(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0);
	glBindVertexArray(0);
}
```


This is the abstraction that we were looking for throughout the book. 

It will be written and tried to use after I've done some chemistry :). 
