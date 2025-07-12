Building ASSIMP: was fairly easy, the usual, just built it with CMake that was provided, then built using the Visual Studio Solution. I creates a DLL, just have to include that into the lib include, and then put the DLL somewhere reachable by the EXE. 


## MESH 
We create using ASSIMP, however, we have to get that information from ASSIMP's own data structures, and put them into our own. 

Minimal Mesh Data: 
```
struct Vertex { 
	glm::vec3 Position;
	glm::vec3 Normal;
	glm::vec3 TextCoords;
};
```

Then we can have Texture as well: 

```
struct Texture{ 
	unsigned int id;
	string type; // diffuse specular etc. 
};
```

Then we can have a `Mesh`
```
class Mesh { 
public: 
	vector<Vertex>            vertices;
	vector<unsigned int>      indices;
	vector<Texture>           textures;


	Mesh(vector<Vertex> verts, vector<unsigned itn> indices, 
		svector<Texture> textures);
	void Draw(Shader & shader);

private: 
	// render data unsigned int VAO, VBO, EBO;

	void setupMesh();
};
```

in `setupMesh` we initialize all the buffers, and then we finally `Draw`. 
The CTOR is pretty self explanatory: But at the end, we just set up mesh;

## Set Up Mesh
Relatively straight forward: 
```
void setUpMesh()
{ 
	glGenVertexArrays(&VAO); 
	glGenBuffers(1, VBO);
	glGenBuffers(1, EBO);

	glBindVertexArray(VAO);
	
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, vertices.size() * sizeof(Vertex), 
		&vertices[0], GL_STATIC_DRAW);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, indices.size() * sizeof(unsigned int),
		&indices[0], GL_STATIC_DRAW);

	// vertex positions
	glEnableVertexAttribArray(0);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)(0));

	// vertex normals if you need them for lighting
	glEnableVertexAttribArray(1);
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, TexCoords));

	// Vertex Texture Coords if you want them
	glEnableVertexAttribArray(2);
	glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(Vertex), 
		(void*)offsetof(Vertex, TexCoords));
}
```

This `offsetof` is amazing. 
We access the representation: 
```
Vertex vertex;
vertex.Position = glm::vec3(0.2f, 0.5f, 0.5f);
vertex.Normal = glm::vec3(0.0f, 1.0f, 0.0f);
vertex.TexCoords = glm::vec2(1.0f, 0.0f);
```
This is of course a property of C++, and their memory layouts, that you can see in Visual Studio. 


## Rendering 
The Draw Method: 

Remember that we need to complete the texturing:

For now, we are skipping this: however, come back to this when we need to know about textures. 

For now: 
```
void Draw(Shader& shader)
{ 
	unsigned int diffuseNr = 1;
	unsigned int specularNr = 1;

	// you might want to look into materials that you have created here
	// after actually getting an obj into the file

	glBindVertexArray(VAO);
	glDrawElements(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0);
	glBindVertexArray(0);
}
```
 Materials would be its own structure. 

# Model
Now we have to work with ASSIMP. 
Create the Load and Translation Model. 

For every Mesh, we will obviously need multiple mesh objs, however, for a Model, there is one model, that will have multiple meshes. 
Think about this as we go forward. 

here is a proprietary structure for Model: 

```
class Model 
{ 
public:
	Model(char* path)
	{ 
		loadModel(path);
	}

private: 
	// model data 
	vector<Mesh> Meshes;
	string directory;

	void loadModel(string path);
	void processNode(aiNode* node, const aiScene* scene);
	Mesh processMesh(aiMesh* mesh, const aiScene* scene);
	vector<Texture> loadMaterialTextures(aiMaterial *mat, 
		aiTextureType type, string typeName);
};
```

In the constructor, we immediately load the model. 

Then of course with `Draw`
```
void Draw(Shader& shader)
{ 
	for(unsigned int i = 0; i < meshes.size(); i++)
		meshes[i].Draw(Shader);
}
```

## Importing the MODEL
```
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>
```
When we use the `loadModel`, from the ctor: 
We use ASSIMP to load the model into a data structure of ASSIMP called a scene object. 
Once we have that object, then we can access all the data that we need. 
```
Assimp::Importer importer;
const aiScene* scene = importer.ReadFile(path, aiProcess_Triangulate | 
													aiProcess_FlipUVs);
```
Declaring an `Importer` object, then using the `ReadFile` using several postprocessing options. 
These will perform extra calculations, operations on the imported data, depending on what we need. 

By setting `aiProcess_Triangulate`, we say that if our model is not all triangles, it will do this for us, that's actually nuts. 
`aiProcess_FlipUVs`, flips the texture coordinates on the y-axis where necessary during processing. Remember that most images are flipped on the y, therefore we might have to flip this thing using ASSIMP. 

There are others as well: 
```
aiProcess_GenNormals - if we dont' have normals

aiProcess_SplitLargeMeshes - splitting large meshes into smallers ones

aiProcess_OptimizeMeshes - doing the reverse by trying to join several meshes into one larger one, reducing drawing calls for optimization
```

Working on that Scene: 
```
void loadModel(string path)
{ 
	Assimp::Importer import;
	const aiScene* scene = import.ReadFile(path, 
		aiProcess_Triangulate | aiProcess_FlipUVs);

	// checking to see if this worked
	if(!scene || scene->mFlags & AI_SCENE_FLAGS_INCOMPLETE || !scene->mRootNode)
	{ 
		cout << "ERROR::ASSIMP::" << import.GetErrorString() << endl;
		return;
	}

	directory = path.substr(0, path.find_last_of('/'));
	processNode(scene->mRootNode, scene);
}
```

After we know that the scene has been loaded. 

After all this we want to process all the Scene's nodes. 
This will fit a recursive structure. 

Each Node (possibly) contains a set of children we want to first process the node in question. 

Nodes are building blocks of a scene graph, which is like a tree-structure. 
The scene graph organizes all the objects in a scene, such as models, lights and cameras, and more, by their spatial and logical relationships. 
Each node contains a transformation matrix that describes how objects within that node are positioned, rotated and scaled compared to their parent Node. 
Moving a parent node will also move the children nodes. 
![[Pasted image 20240815133217.png]]

[Data Structure](https://learnopengl.com/Model-Loading/Assimp)

![[Pasted image 20240815133933.png]]
A scene graph roughly looks like this. 
![[Pasted image 20240815133953.png]]
you can see how each node will have a bunch of different models in it. 

Then our `processNode(aiNode * node, const aiScene* scene)`. 
Each node contains a set of mesh indices where each index points to a specific mesh in the scene object. 
The scene contains all the meshes, then the nodes will give us a hierarchy of those meshes. 
The nodes that don't contain all the mesh data, that's the scene, the scene will contain all the raw data. 
Each node can reference one or more meshes that are held in the scene, they also contain the spatial and logical arrangement of the scene elements. 
```
void processNode(aiNode * node, const aiScene* scene)
{ 
	// process all the ndoe's meshes (if any) 
	// they point to the actual raw data that is found in the scene
	for(unsigned int  i = 0; i < node->mNumMeshes; i++)
	{ 
		aiMesh* mesh = scene->mMeshes[node->mMeshes[i]];
		// getting the actual mesh data from the scene
		// then pushing that data into out mesh vector
		meshes.push_back(processMesh(mesh, scene));
	}
	
	// then we have to go through each of the children, recursively
	for(unsigned int i = 0; i < node->mNumChildren; ++i)
	{ 
		processNode(node->mChildren[i], scene);
	}
}
```
We have to pass in the scene, so that we have the actual raw data. 


If the node has meshes, then we retrieve them from the scene and add them to the vector. 
We do that for each of the meshes. 
Then the recursive aspect, is that we go through each of the nodes one by one, remember that the return aspect of this is that we return to that point, increment by one. Then we do the next one, and we get all the mesh data, however, if there are no children then `0 < 0` then we just don't recurse anything. 

Now you may have noticed that you migh tas well just go through the scene's meshes, without doing all this. 
The node defines parent - child relationship between meshes. 
This parent child relationship is easier to create when it's already set up from the get go. 

Right now, we're not hard coding the system at all. 
The node like relationships are created by the artists who created the models. 

## Assimp to Mesh
Now that we have a the aiMesh, then we have to `processMesh(mesh, scene)` so that we can pushback that return value. 

Just translate the `aiMesh` into the `Mesh` object of our own. 
```
Mesh processMesh(aiMesh *mesh, const aiScene* scene)
{ 
	vector<Vertex> vertices;
	vector<unsigned int> indices;
	vector<Texture> texture;

	for(unsigned int = 0; i < mesh->mNumVertices; i++)
	{ 
		Vertex vertex;
		// process vertex positions, normals and vertex coordinates
		[...]
		vertices.push_back(vertex);
	}
	// process indices
	[...]

	// process material
	[...]

	if(mesh->mMaterialIndex >= 0)
	{ 
		[...]
	}
	return Mesh(vertices, indices, textures);
}
```

We need all the Vertex data, the mesh's indices, and finally retrieve the relevant material data. 
Retrieving the vertex data is simple: 
```
glm::vec3 vector; 
vector.x = mesh->mVertices[i].x;
vector.y = mesh->mVertices[i].y;
vector.z = mesh->mVertices[i].z;
vertex.Position = vector;
```

ASSIMP has its own data types for vectors, matrices and strings etc., they don't convert that well to glm's data types. 


ASSIMP calls their vertex position array `mVertices` which isn't the most intuitive name. 
Here we have the mesh, and we create a vertex using the system above. 

Then we get the normals: 
```
glm::vec3 vector;
vector.x = mesh->mNormals[i].x;
vector.y = mesh->mNormals[i].y;
vector.z = mesh->mNormals[i].z;
```

Texture coordinates a little bit different: as ASSIMP allows a model to have up to 8 different texture coordinates per textures: 
We might not always have texture coordinates as well: 
```
if (mesh->mTextureCoords[0]) // does the mesh contain texture coordinates
{ 
	glm::vec3 vec;
	vec.x = mesh->mTextureCoords[0][i].x;
	vec.y = mesh->mTextureCoords[0][i].y;
	vertex.TexCoords = vec;
}
```

Now we just have one vertex, there can be many many more :'). 

A face contains the indices of the vertices we need to draw in that order for that primitive. 
Then basically: 
```
for(unsigned int = 0; i < mesh->mNumFaces; i++)
{ 
	aiFace face = mesh->mFaces[i];
	for(unsigned int j = 0; j < face.mNumIndices; j++)
	{ 
		indices.push_back(face.mIndices[j]);
	}
}
```
Now we have the ability to `glDrawElements`: 

## Material
A mesh contains an index to a material object: 
To get the material of a mesh, we need to get the scene's `mMaterials` array. 
We need to query whether the Mesh has a material or not: 
```
if(mesh->mMaterialIndex >= 0)
{ 
	aiMaterial* material = scene->mMaterials[mesh->mMaterialIndex];
	vector<Texture> diffuseMaps = loadMaterialTextures(material, 
		aiTextureType_DIFFUSE, "texture_diffuse");
	textures.insert(textures.end(), diffuseMaps.begin(), diffuseMaps.end());
	vector<Texture> specularMaps = loadMaterialTextures(material, 
		aiTextureType_SPECULAR, "texture_specular");
	textures.insert(textures.end(), specularMaps.begin(), specularMaps.end());
}
```

`loadMaterialTextures` is a helper function, that iterates over all the texture locations of the given texture type, retrieves the texture files location and then loads and generates the textures and stores the information in a Vertex struct, it looks like this: 
```
vector<Texture> loadMaterialTextures(aiMaterial * mat, aiTextureType type, 
	string typeName)
{ 
	vector<Texture> textures;
	for(unsigned int = 0; i < mat->GetTextureCount(type); i++)
	{ 
		aiString str;
		mat->GetTexture(type, i, &str);
		Texture texture;
		texture.id = TextureFromFile(str.c_str(), directory);
		texture.type = typeName;
		texture.path = str;
		textures.push_back(texture);
	}
	return textures;
}
```
Note that we are assuming that we are getting the texture from the same file that the models are located in, if that is different, then we need a different path. 
Remember that we use `stb_image.h`. 

### Optimization
There is an optimization, here we can load all the textures globally, and then we casn see to them as need be, rather than loading the same texture for perhaps various meshes, which is just loading the same thing over and over again. 

