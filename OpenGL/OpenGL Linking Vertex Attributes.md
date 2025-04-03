Telling the GPU what the format of the vertices we have sent in is. 

Allowing for great variety, however, we do have to think about how we are going to use it, and we have to understand how they all link together and how it all works.

This is our, rather simple vertex, with just position attributes. 

![[Pasted image 20240605144022.png]] 
Our floats are 4 bytes each. 
The values are tightly packed, and no other attributes, meaning that our vertices have a stride of 12 bytes, 4 * 3 bytes. 

So let's tell OpenGL how we are going to interpret the vertex data, using `glVertexAttribPointer`. 
```
glVertexAttribPointer(0 , 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
```
The first param, specifies which vertex attribute we want to configure, remembering that we specified the location of the position in our vertex shader to be 0. This sets the location of the vertex attribute to 0, therefore we will pass in 0 as well. 
Then we pass in how many components that attribute will be, which in this case `vec3`, therefore 3. 

Then we are specifying the type of data, which is `GL_FLOAT`. A `vec*` in GLSL consists of floating point values. 

The next is for normalizing, however, we don't need to in this case, therefore we won't normalize. 

#openglstride
The fifth is known as `stride`. 
Telling us the space between consecutive vertex attributes. 
See above. 
When we have more vertex attributes, we have to carefully define the spacing between each vertex attribute. 
The last parameter is of `void*`, requiring the weird cast. This is the offset of the position in the data, since this is our position, and it is right at the start of the data, the value will just be 0. 

Now we need to enable it, using what we have given VBO:
```
// 0. copy our vertices array in a buffer for openGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 1. then set the vertex attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
// last attrib is the pointer to the first case of it

// 2. use our shader program when we want to render an object
glUseProgram(shaderProgram);

// 3. now draw the object
someOpenGLFunctionThatDrawsOurTriangle();
```
Now in order to do this loads of times, we would have to write this out for every single vertex attribute etc. 

What if we could make this easier by having an object, containing all this information, and then just using that object every time we want to restore this state. 

