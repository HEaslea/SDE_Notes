We create memory on the GPU we will be storing the data, configure how OpenGL will be interpreting that memory and then specifying how to send that data to the graphics card. 
Then the shaders will process as much vertices as well tell it to. 

Vertex Buffer Objects (VBO), which are objects that allow us to send a large group of them to the GPU at one time, keeping them there if there is enough memory left. 
Meaning that we don't have to send one vertex at a time. 

This is a relatively slow process, sending data to the GPU, remember from the game engine book. 
We want to send as much as we can in one go. 

When there, the shaders will have almost instant access to it. 

[[OpenGL Objects]]
Like other objects, this buffer (object) will have a unique ID corresponding to that buffer, so we can generate one  with this:

```
unsigned int VBO; 
glGenBuffers(1, &VBO);
```

There are many buffer objects, and the buffer type of a vertex buffer objects is `GL_ARRAY_BUFFER`. 
We can bind many buffers at once, as long as they have differing types. 
This makes total sense, as the context will only have one of those "types". 
`glBindBuffer(GL_ARRAY_BUFFER, VBO)`, the type of buffer we are binding to, then the buffer we are actually assigning there. 

From this point, after binding it, any call to `GL_ARRAY_BUFFER`, and if we had bound to others, then calls to those too, will be acting on that currently bound buffer, in this case `VBO`. 
Therefore we can make a call to `glBufferData` to give that buffer object data. 


`glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);`
Here, you might want to do, `sizeof(decltype(vertices[0]))* noOfVertices` as that will give the exact figure as the other can sometimes go wrong. 

Remember that this size, will be in bytes, most of them are. 


![[Pasted image 20240605000647.png]]
Now, there is the vertex data on the GPU, and will be managed by the vertex buffer object named `VBO`. 

