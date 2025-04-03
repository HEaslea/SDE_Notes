GPU with data, store memory on VRAM, shader on gpu, that will read data and output to screen. 

Vertex Buffer, buffer on GPU, shader takes from this, and it will need to know the layout, what type of data is in the buffer? 

The exact same as writing, float x; the compiler will know that it is a float in that memory address. 

Type Punning follows that we reinterpret that data. 

Vertex contains way more data, like in the game engine book. 

When we have an array of data, which part relates to which attribute? eg. colour, position, normal etc. 


`glVertexAttribPointer`: 
[docs.Gl link](https://docs.gl/gl4/glVertexAttribPointer)
The shader will read from an index. 
So the index argue, is that of index to the generic vertex attribute to be modified. 
Say we have position, texture coordinate, and normal, say that we then want to index them at 0, 1, 2, each attribute at each index. 
So we argue index 2, we know that that is the normal. 

The size is number of datum per attribute. 

Normalized, if we have changed our float from 0-255 to 0-1. Which is the idea of normalizing. In our case, we are not normalizing ourselves, therefore it will be false. 

Stride - amount of bytes between each vertex. Ie. the size of each vertex. The size of one vertex to the next, which will be the size of our vertex. 

Pointer - pointer into the actual attributes, inside the space of the vertex, say we have position, texture coordinate and normal. So basically the stride but for in the vertex, the number of bytes between the attributes. There are macros for this. 

The trick is that if we get them wrong, then we will get some weird artefacts. 

The written part is this: 
`glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, sizeof(float) * 2, 0)`
To note that if the pointer was anything but 0, say it was 8, then we could write something like this `(const void*) 8`. 

There is something that we have to do to move on: 
we have to enable this vertex attribute, this will be forgettable if we do it at the beginning and then we forget about it forever, however, it has to be done. 
[Enable Vertex Attribute](https://docs.gl/gl4/glEnableVertexAttribArray)

Remember that openGL is essentially a state machine. We can do this in any order really. 
There is a free pdf that you can read however, that is really stunning: 
[Free Book on OpenGL](https://learnopengl.com/book/book_pdf.pdf)

